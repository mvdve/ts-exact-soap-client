# Exact Globe soap client

Simple wrapper for soap client which enables it to connect to the Exact entity services via the soap interface.

## Examples:

```typescript
import * as soap from "ts-exact-soap-client";
import axios from "axios";

const soapConfig: soap.Config = {
    soapHost: "http://192.168.1.1:8010",
    dbHost: "SERVER_NAME\\host_name",
    dbName: "database_name",
    domain: "domain_name",
    password: "password",
    userId: "username"
}
try {
    // Depending on the mode, the correct WSDL file is loaded.
    const client = await soap.createClient("single", soapConfig);
    
    // Send order line
    const linePropertyData: soap.InputPropertyData[] = [];
    linePropertyData.push({name: "ItemCode", value: "product-sku"});
    linePropertyData.push({name: "Quantity", value: 10});

    const transactionKey = await soap.create(client, "SalesOrderLine", linePropertyData);
    // Use transaction key for next line and header.
    
    // Load product
    // Re-use the client to prevent an extra call for the wsdl file.
    const product = await retrieve(client, "Item", [{name: "ItemCode", value: "itemcode"}]);
} catch (err) {
    // Extract errors from Entity services response
    if (axios.isAxiosError(err) && err.response) {
        const message = soap.extractErrorMessage(String(err.response.data));
        // Use error message
    }   
}
```