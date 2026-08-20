1. **Overview** — *The company, Contoso Retail, needs to connect two of their locations using Azure. Their head office `(HQ-VNet)` and their branch office `(Branch-VNet)` will be securely connected through `VNet peering`.*
2. **Architecture** 
```mermaid
graph TB
    Internet((Internet))

    subgraph HQVNET["HQ-VNet — 10.10.0.0/16"]
        subgraph SWEB["snet-web — 10.10.1.0/24"]
            VMWEB["VM-Web<br/>Public IP"]
        end
        subgraph SAPP["snet-app — 10.10.2.0/24"]
            VMAPP["VM-App<br/>Private only"]
        end
        subgraph SDATA["snet-data — 10.10.3.0/24"]
            NODATA["No VM — NSG only"]
        end
    end

    subgraph BRVNET["Branch-VNet — 10.20.0.0/16"]
        subgraph SBRANCH["snet-branch — 10.20.1.0/24"]
            VMBRANCH["VM-Branch<br/>Private only"]
        end
    end

    Internet -->|"80/443 open, 22 from admin IP only"| VMWEB
    VMWEB -->|"port 8080"| VMAPP
    VMAPP -.->|"port 1433 — reserved, no VM"| NODATA
    HQVNET <-->|"VNet Peering"| BRVNET
    VMWEB -->|"22 — allowed by default VirtualNetwork rule"| VMBRANCH
```
3. **Design decisions** — *Each tier is isolated using `Network Security Groups` following least-privilege principles. A resource can only talk to what it explicitly needs to.*
4. **Build steps** — *Created a resource group `(rg-cloudnet-lab)` to contain **all resources** for clean deprovisioning. Created both VNets and necessary subnets. Created NSGs and attached them to networks and subnets. Created three VMs, and peered the networks together. Confirmed successful connectivity.*
5. **Testing and results** — *Tested: web tier is reachable from the internet, app tier is reachable only from the web subnet, and peering routes traffic and the default NSG rules allow it. Results are in Documentation folder.*
6. **Challenges and troubleshooting** — *The free account subscription was blocking VM creation due to spending limit protection. I upgraded to pay-as-you-go, but the default East US region did not offer any available VM sizes. I selected West US region, and could proceed with VM creation without further issue.*
7. **Cleanup** — *Resource group `rg-cloudnet-lab` and all resources contained within are now deleted.*