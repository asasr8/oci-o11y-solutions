# Fusion Cloud Application monitored by O&M Services

Using Logging Analytics we can monitor the products of the Fusion Application. For the list of available Oracle-defined sources for Fusion Applications, see [Oracle-defined Sources](https://docs.oracle.com/en-us/iaas/logging-analytics/doc/oracle-defined-sources.html#GUID-7DB43543-E971-4797-8971-DC9700326CAA).

## Custom sources setup :

Let us see how we can create Custom sources for Fusion products

* Identify the product you want to collect the audit logs for and enable its audit trail report in Fusion Apps.
* In our case the product was Cash Management and Business Object Type was External Bank Account
*	Next, we searched for Cash Management in [here](Reference)
 and found the web application name to be 'Payables'.
* Use Audit Setup API to fetch the View Objects associated with the product/web app.
  * Sample Curl command :
  <pre>
  curl --location 'https://BASE_URL_HERE/fscmRestApi/fndAuditRESTService/audittrail/get-auditsetup' \
    --header 'Content-Type: application/json' \
    --header 'Authorization: Basic ******' \
    --data '{
        "application": "Payables"
    }'
    </pre>
* This will give you the available view objects. Search for External Bank Account in the above JSON response. You will get the associated View Object. Look for parent VOs.
  * Sample View Object inside JSON response:
  <pre> "viewObject" : "oracle.apps.********.core.view.ExternalBankAccountVO" </pre>

* Use the product and business object type we got above to fetch the audit logs using REST API
  * Sample Curl command :
    <pre>
    curl --location 'https://BASE_URL_HERE/fscmRestApi/fndAuditRESTService/audittrail/getaudithistory?pageSize=50&pageNumber=1' \
    --header 'Content-Type: application/json' \
    --header 'Authorization: Basic ******' \
    --data '{
        "fromDate": "2024-01-02 00:00:00",
        "toDate": "2024-01-07 00:00:00",
        "product": "Payables",
        "includeAttributes": true,
        "attributeDetailMode": true,
        "includeImpersonator": true,
        "includeExtendedObjectIdentiferColumns": true,
        "businessObjectType": "oracle.apps.********.core.view.ExternalBankAccountVO",
        "includeChildObjects":true
        }'
      </pre>
* Once we have the output we can use the REST API to setup ingestion.
* We can follow the steps from this [document](https://docs.oracle.com/en-us/iaas/logging-analytics/doc/set-rest-api-log-collection.html#LOGAN-GUID-17E91D5F-5EE3-42EB-8214-1D4597432D6C)
