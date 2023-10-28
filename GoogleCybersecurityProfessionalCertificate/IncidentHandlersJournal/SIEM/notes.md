# Log Source Buttercup Games
`mailsv` - Buttercup Games' mail server. Examine events generated from this host. \
`www1` - This is one of Buttercup Games' web applications. \
`www2` - This is one of Buttercup Games' web applications. \
`www3` - This is one of Buttercup Games' web applications. \
`vendor_sales` - Information about Buttercup Games' retail sales.

## Splunk Search Query:
Searching for failed login attempts made to Buttercup Games mail server. \
`index=main host=mailsv fail* root`