# NoSQL Injection — Defensive Guide

> **Purpose:** This document explains what NoSQL injection is, how to recognize vulnerable patterns, and — most importantly — how to prevent and detect it safely.  
> **Important:** Do not use this guide to attack systems. Only test on systems you own or where you have explicit permission.

---

## Table of contents
- [What is NoSQL injection?](#what-is-nosql-injection)
- [Why it matters](#why-it-matters)
- [Common vulnerable patterns (high level)](#common-vulnerable-patterns-high-level)
- [Secure coding and prevention checklist](#secure-coding-and-prevention-checklist)
- [Secure coding examples (defensive patterns)](#secure-coding-examples-defensive-patterns)
- [Detection, logging, and monitoring](#detection-logging-and-monitoring)
- [Safe testing & learning resources](#safe-testing--learning-resources)
- [Incident response & disclosure](#incident-response--disclosure)
- [Further reading](#further-reading)

---

## What is NoSQL injection?
NoSQL injection is a class of injection vulnerability that targets applications using NoSQL databases (for example: document stores, key-value stores, wide-column stores). Like SQL injection, an attacker manipulates input that is used to construct database queries so that the behavior of the query changes in unintended ways.

> Key point: the exploitation technique varies by database type and driver API, but the root cause is the application trusting or directly embedding untrusted input into database queries or query shapes.

---

## Why it matters
- NoSQL databases are often used to store authentication, authorization, and business-critical data — a successful exploitation can lead to data leakage, privilege escalation, or unauthorized data modification.
- Modern drivers and ORMs provide rich query languages and powerful operators; if user input is allowed to control operators or query structure, the attack surface increases.

---

## Common vulnerable patterns (high level)
> These are *patterns to avoid* — explained at a conceptual level (no exploit strings).

- **Directly parsing user-provided JSON into a query object.**  
  If you parse user input as JSON and pass it directly into a query builder, attackers may include operators or keys the application did not intend.

- **Using user input to choose query operators.**  
  Allowing clients to specify operator names (e.g., `$ne`, `$gte`, `$or`) or to submit raw filter fragments increases risk.

- **String concatenation to build queries.**  
  Building query expressions by concatenating strings or templates that include user input can inadvertently change query meaning.

- **Poor input validation or overbroad query parameters.**  
  Accepting free-form filter objects or allowing arbitrary fields without validation/whitelisting.

- **Excessive database permissions.**  
  Applications running with broad privileges can cause greater harm if a query is manipulated.

---

## Secure coding and prevention checklist
Use multiple layers of defense — prevent, limit, and detect.

1. **Do not trust user input.** Validate and canonicalize every input used in queries.
2. **Use parameterized APIs / driver abstractions.** Prefer high-level driver methods and avoid building raw query strings or objects from untrusted input.
3. **Implement field whitelisting / allowlists.** Only allow filtering on known, expected fields; reject or ignore unknown fields.
4. **Disallow users from supplying operators.** Treat operators (e.g., `$or`, `$regex`) as server-side-only; don’t accept them from clients.
5. **Type-check and constrain values.** Enforce data types (string, number, boolean), allowed ranges, and length limits.
6. **Limit result size and permissions.** Apply `limit`, projections, and least-privilege database accounts (separate read/write roles).
7. **Avoid unsafe JSON parsing into queries.** If accepting JSON, strictly validate its schema before use.
8. **Use query-building utilities safely.** When building dynamic queries, construct them server-side using validated inputs, not by interpolating raw input.
9. **Sanitize user-controlled data used in indexed keys or identifiers.** Avoid allowing arbitrary user-controlled key names.
10. **Keep DB and driver libraries updated.** Use maintained drivers; benefit from security fixes.

---

## Secure coding examples (defensive patterns)

### 1) Whitelisting fields before building a query (conceptual example)
```javascript
// Node.js / pseudo-code (defensive)
const allowedFilters = ['username', 'email', 'status', 'createdBefore'];
function buildSafeQuery(clientFilter) {
  const q = {};
  for (const key of Object.keys(clientFilter)) {
    if (!allowedFilters.includes(key)) continue; // ignore unexpected keys
    const val = clientFilter[key];

    // enforce type/format per field
    if (key === 'username' || key === 'email') {
      if (typeof val !== 'string') continue;
      q[key] = val; // safe assignment after validation
    } else if (key === 'status') {
      if (!['active','disabled'].includes(val)) continue;
      q[key] = val;
    } else if (key === 'createdBefore') {
      const ts = Date.parse(val);
      if (Number.isNaN(ts)) continue;
      q['createdAt'] = { $lt: new Date(ts) }; // server-decided operator
    }
  }
  return q;
}
```

## Cheatsheet

PATCH request sent to `https://example.com/rest/products/reviews`
this will update all comments not equal to the provided id
```json
// PATCH /rest/products/reviews 
{
    "message":"test comment",
    "id":{
        "$ne": -1
    }
}
```