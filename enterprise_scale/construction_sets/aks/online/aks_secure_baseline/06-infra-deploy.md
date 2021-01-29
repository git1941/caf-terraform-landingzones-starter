Now you have reached the stage where you have reviewed the configuration files and can proceed with the Terraform deployment.

```bash
# Script to execute from bash shell

cd enterprise_scale/construction_sets/aks

configuration_folder=online/aks_secure_baseline/configuration

# Define the configuration files to apply
parameter_files=("\
  -var-file=${configuration_folder}/global_settings.tfvars \
  -var-file=${configuration_folder}/resource_groups.tfvars \
  -var-file=${configuration_folder}/networking.tfvars \
  -var-file=${configuration_folder}/nsg.tfvars \
  -var-file=${configuration_folder}/firewalls.tfvars \
  -var-file=${configuration_folder}/firewall_application_rule_collection_definition.tfvars \
  -var-file=${configuration_folder}/firewall_network_rule_collection_definition.tfvars \
  -var-file=${configuration_folder}/public_ips.tfvars \
  -var-file=${configuration_folder}/keyvaults.tfvars \
  -var-file=${configuration_folder}/iam_aad.tfvars \
  -var-file=${configuration_folder}/iam_role_mappings.tfvars \
  -var-file=${configuration_folder}/aks.tfvars \
  -var-file=${configuration_folder}/route_tables.tfvars \
  -var-file=${configuration_folder}/diagnostics.tfvars \
  ")

export TF_VAR_logged_user_objectId=$(az ad signed-in-user show --query objectId -o tsv)

# Load the CAF module and related providers
terraform init -upgrade



terraform apply -var-file=${configuration_folder}/global_settings.tfvars \
  -var-file=${configuration_folder}/resource_groups.tfvars \
  -var-file=${configuration_folder}/networking.tfvars \
  -var-file=${configuration_folder}/nsg.tfvars \
  -var-file=${configuration_folder}/firewalls.tfvars \
  -var-file=${configuration_folder}/firewall_application_rule_collection_definition.tfvars \
  -var-file=${configuration_folder}/firewall_network_rule_collection_definition.tfvars \
  -var-file=${configuration_folder}/public_ips.tfvars \
  -var-file=${configuration_folder}/keyvaults.tfvars \
  -var-file=${configuration_folder}/iam_aad.tfvars \
  -var-file=${configuration_folder}/iam_role_mappings.tfvars \
  -var-file=${configuration_folder}/aks.tfvars \
  -var-file=${configuration_folder}/route_tables.tfvars \
  -var-file=${configuration_folder}/diagnostics.tfvars \
  -var-file=${configuration_folder}/certificate_requests.tfvars \
  -var-file=${configuration_folder}/keyvaults.tfvars \
  -var-file=${configuration_folder}/iam_managed_identities.tfvars \
  -var-file=${configuration_folder}/private_dns.tfvars \
  -var-file=${configuration_folder}/domain.tfvars \
  -var-file=${configuration_folder}/agw.tfvars \
  -var-file=${configuration_folder}/agw_application.tfvars \
  -var-file=${configuration_folder}/bastion.tfvars 

# Create the plan
terraform plan \
  -out="online-aks.tfplan" \
  ${parameter_files// / }

# Trigger the deployment of the resources
terraform apply "online-aks.tfplan"

# (When needed) Destroy the resources
terraform destroy \
  ${parameter_files// / }

# or if you are facing destroy issues
terraform destroy \
  ${parameter_files// / } \
  -refresh=false

```