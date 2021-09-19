# AzureFuncApp
def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')
    
    # Parsing the input parameter
    ip_file_name = req.params.get('ipFileName')
    op_file_name = req.params.get('opFileName')
    client_id = req.params.get('applicationId')
    client_secret = req.params.get('authenticationKey')
    tenant_id = req.params.get('tenantId')
    

    # Azure subscription ID
    subscription_id = 'XXXXX'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = 'XXXXX'

    # The data factory name. It must be globally unique.
    df_name = 'XXXXX'

    # Specify your Active Directory client ID, client secret, and tenant ID
    # creating adf client using the authentication passed from input JSON
    credentials = ClientSecretCredential(client_id=client_id, client_secret=client_secret, tenant_id=tenant_id) 
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)
    
    #pipeline name
    p_name = 'XXXXX'
    
    # creating paramter to be passed to pipeline
    # Pls note the key name should match exactly paramter name provided in ADF pipeline
    # e.g. p_ip_file is input file name provided paramter in ADF pipeline
    parameters1 = {"p_ip_file" : ip_file_name,
                    "p_op_file" : op_file_name
                    }
    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name,parameters=parameters1)
    
    if run_response:
        return func.HttpResponse(f"{run_response},{parameters1}")
    else:
        return func.HttpResponse(
             "Failed - This HTTP triggered function executed unsuccessfully",)
