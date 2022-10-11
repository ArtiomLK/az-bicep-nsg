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
--priority 100 \
--name AllowRdpInbound \
--source-port-ranges "*" \
--destination-port-ranges 3389 \
--protocol "TCP" \
--source-address-prefixes "*" \
--destination-address-prefixes VirtualNetwork \
--access Allow \
--nsg-name $nsg_n \
--resource-group $rg

# -----------------------------------------------------------------------------------------------
#  SSH
# ------------------------------------------------------------------------------------------------
az network nsg rule create \
--direction Inbound \
--priority 200 \
--name AllowSshInbound \
--source-port-ranges "*" \
--destination-port-ranges 22 \
--protocol "TCP" \
--source-address-prefixes "*" \
--destination-address-prefixes VirtualNetwork \
--access Allow \
--nsg-name $nsg_n \
--resource-group $rg
```
