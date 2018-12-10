# Errors
The NEO dAPI will provide these basic errors. It is up to the wallet provider to provide additional information if they choose:

Error Type | Meaning
---------- | -------
NO_PROVIDER | Could not find an instance of the dAPI in the webpage
CONNECTION_REFUSED | The dAPI provider refused to process this request
RPC_ERROR | An RPC error occured when submitting the request
SEND_ERROR | An error occured while attempting to send
MALFORMED_ADDRESS | An input address was invalid
CANCELED | The user chose to cancel the action
