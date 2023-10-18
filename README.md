# Network Security Group (NSG)

## CLI

```bash
l="eastus2";                                                            echo $l
env="prod";                                                             echo $env
project="bicephub";                                                     echo $env
tags="env=$env project=$project";                                       echo $tags
rg="rg-nsg-$project-$env-$l";                                           echo $rg
sub_id='########-####-####-####-############';                          echo $sub_id

# Default NSG
# nsg_n="nsg-default-$project-$env-$l";                                   echo $nsg_n
# AGW NSG
nsg_n="nsg-agw-$project-$env-$l";                                       echo $nsg_n


# RG
az group create \
--subscription $sub_id \
--name $rg \
--location $l  \
--tags $tags

# NSG
az network nsg create \
--name $nsg_n \
--subscription $sub_id \
--resource-group $rg \
--location $l \
--tags $tags

# -----------------------------------------------------------------------------------------------
#  RDP
# -----------------------------------------------------------------------------------------------
az network nsg rule create \
--direction Inbound \
--source-address-prefixes "*" \
--source-port-ranges "*" \
--destination-address-prefixes VirtualNetwork \
--destination-port-ranges 3389 \
--protocol "TCP" \
--access Allow \
--priority 100 \
--name AllowRdpInbound \
--nsg-name $nsg_n \
--subscription $sub_id \
--resource-group $rg

# -----------------------------------------------------------------------------------------------
#  SSH
# -----------------------------------------------------------------------------------------------
az network nsg rule create \
--direction Inbound \
--source-address-prefixes "*" \
--source-port-ranges "*" \
--destination-address-prefixes VirtualNetwork \
--destination-port-ranges 22 \
--protocol "TCP" \
--access Allow \
--priority 200 \
--name AllowSshInbound \
--nsg-name $nsg_n \
--subscription $sub_id \
--resource-group $rg

# -----------------------------------------------------------------------------------------------
#  AGW V1
# -----------------------------------------------------------------------------------------------
# AllowGatewayManagerInbound
az network nsg rule create \
--direction Inbound \
--priority 300 \
--destination-port-ranges 65503-65534 \
--protocol TCP \
--source-address-prefixes GatewayManager \
--destination-address-prefixes "*" \
--access Allow \
--name AllowGatewayManagerInbound \
--nsg-name $nsg_n \
--subscription $sub_id \
--resource-group $rg

# -----------------------------------------------------------------------------------------------
#  AGW V2
# -----------------------------------------------------------------------------------------------
# AllowGatewayManagerInbound
az network nsg rule create \
--direction Inbound \
--priority 400 \
--destination-port-ranges 65200-65535 \
--protocol TCP \
--source-address-prefixes GatewayManager \
--destination-address-prefixes "*" \
--access Allow \
--name AllowGatewayManagerInbound \
--nsg-name $nsg_n \
--subscription $sub_id \
--resource-group $rg

# -----------------------------------------------------------------------------------------------
#  FD
# -----------------------------------------------------------------------------------------------

# AllowAzureFrontDoor.BackendInbound
az network nsg rule create \
--direction Inbound \
--priority 500 \
--destination-port-ranges 443 80 \
--protocol TCP \
--source-address-prefixes AzureFrontDoor.Backend \
--destination-address-prefixes VirtualNetwork \
--access Allow \
--name AllowAzureFrontDoor.Backend \
--nsg-name $nsg_n \
--subscription $sub_id \
--resource-group $rg
```

## Additional Resources

- AGW
- [MS | Application Gateway infrastructure configuration | Required security rules][1]

[1]: https://learn.microsoft.com/en-us/azure/application-gateway/configuration-infrastructure#required-security-rules