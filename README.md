# Network Security Group (NSG)

## CLI

```bash
l="eastus2";                                  echo $l
tags="env=dev app=nsg";                       echo $tags
rg="rg-nsg-example";                          echo $rg

nsg_n="nsg-example";                          echo $nsg_n


# RG
az group create \
--name $rg \
--location $l  \
--tags $tags

# NSG
az network nsg create \
--name $nsg_n \
--resource-group $rg \
--location $l \
--tags $tags

# -----------------------------------------------------------------------------------------------
#  RDP
# ------------------------------------------------------------------------------------------------
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
--resource-group $rg

# -----------------------------------------------------------------------------------------------
#  SSH
# ------------------------------------------------------------------------------------------------
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
--resource-group $rg

# -----------------------------------------------------------------------------------------------
#  AGW V2
# ------------------------------------------------------------------------------------------------
# AllowGatewayManagerInbound
az network nsg rule create \
--name AllowGatewayManagerInbound \
--direction Inbound \
--resource-group $rg \
--nsg-name $nsg_n \
--priority 300 \
--destination-port-ranges 65200-65535 \
--protocol TCP \
--source-address-prefixes GatewayManager \
--destination-address-prefixes "*" \
--access Allow

# AllowAzureFrontDoor.BackendInbound
az network nsg rule create \
--name AllowAzureFrontDoor.Backend \
--direction Inbound \
--resource-group $rg \
--nsg-name $nsg_n \
--priority 200 \
--destination-port-ranges 443 80 \
--protocol TCP \
--source-address-prefixes AzureFrontDoor.Backend \
--destination-address-prefixes VirtualNetwork \
--access Allow
```

## Additional Resources

- AGW
- [MS | Application Gateway infrastructure configuration | Required security rules][1]

[1]: https://learn.microsoft.com/en-us/azure/application-gateway/configuration-infrastructure#required-security-rules