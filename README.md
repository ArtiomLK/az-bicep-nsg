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
```
