
## Objective

Build incrementally an ecosystem capable of testing a digital twin of our network. Both the routing components and the networking systems.

## Sub-Projects

1. [ippal-mdm](https://github.com/nosportugal/ippal-mdm)
	- Goal: implement a mediation layer capable of abstracting the multivendor particularities of the different router vendor implementations
	- Features:
		- Provision configs with gNMI Set (partially implemented)
		- Audit capabilities with gNMI Get (tested)
		- Expose capabilities vendor agnostically (todo)
	- Tasks:
		1. Make clabprovision execute in parallel and test it.
		2. Add the MPLS config templates to this scenario
		3. Create a small CI/CD pipeline
		4. Build a network topology in ArangoDB. This DB was chosen before Neo4j because it allows free production use. For the paid version Neo4j is the best option for a graph only usecase. 
- [ippal-infra-templates](https://github.com/nosportugal/ippal-infra-templates)
	- Goal: push our documentation to git, in order to have collaborative edition and versioning.
	- Features:
		- Markdown files for documenting the protocols used in our infrastructure and our architecture decisions (partially implemented)
		- Explore ways to generate diagrams in code so we can have versioned diagrams (todo)
		- Static web page generated from our documentation (todo)
	- Tasks:
		1. Push all ISIS info from asr9k LLD to the repo (todo)
		2. Make corrections to SROS one, like adding the net parameter (waiting for new license)
	- Issues/Limitations:
		- SR Linux configuration documentation does not match the device configuration for the same version. Because of this the IS-IS template was aborted.
		- SR Linux and SROS have different configuration templates
- [ippal-emulated-labs](https://github.com/nosportugal/ippal-emulated-lab)
	- Goal: have a series of lab environments statically coded that can be quickly turned on and used by our teammates.
	- Features:
		- Use ippal-mdm to make the scenarios' base configurations
		- Configure using our templates at ippal-infra-templates
	- Tasks:
		1. Push on the other 2 projects first
		2. Make container and vm lab with a baseline ISIS scenario
		4. Integrate with our telemetry engine
		5. Add grafana to visualize telemetry data
		6. Make srv6 xr-only lab
- Related Work
	- OpenConfig:
		- yang rpcs in gNMI
	- Containerlab:
		- support for sonic-vm
		- support for ocnos-vm
		- gRPC with TLS and certificate management
	- Candidates to be emulated
		- 6WNOS - 6WIND (todo +)
		- Ensemble Connector - Adtran (todo)
		- vEOS - Arista (done)
		- cEOS - Arista (done)
		- SR Linux - Nokia (done)
		- SROS - Nokia (done)
		- XRv9K - Cisco (done)
		- XRd - Cisco (done)
		- Junos OS Evolved - Juniper (todo ++)
		- Hedgehog - Hedgehog (todo)
		- ArcOS - Arrcus (todo ++)
		- DNOS - Drivenets (todo ++)
		- OcNOS - IP Infusion (todo ++)
		- VyOS - VyOS (todo)
		- Fullstack - RtBrick (todo +)
		- Cumulus - NVIDIA (todo +)
		- SONIC - Linux Foundation (todo +) 