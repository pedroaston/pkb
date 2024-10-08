## Objective

Build incrementally an ecosystem capable of testing a digital twin of our network. Both the routing components and the networking systems.

## Sub-Projects

1. [ippal-mdm](https://github.com/nosportugal/ippal-mdm)
	- Goal: implement a yang-based mediation layer capable of abstracting the multivendor particularities of the different router vendor implementations and enable network programmability.
	- Issues:
		- unified model keychain on 7.11.2
		- ygot has a limitation regarding repeated namespaces ([issue](https://github.com/openconfig/ygot/issues/834))
	- Tasks:
		1. Generate Nokia SROS adaptors
		2. Setup Nokia Mediator
	- Backlog:
		- Switch from list to map in json normalized (e.g adjacencies)
		- Understand how to divide state and config
		- Add topology validation to IGPSegment validation (sh isis topology)
		- Explore integration with ArangoDB
		- Explore integration with Netbox
- [ippal-infra-templates](https://github.com/nosportugal/ippal-infra-templates)
	- Goal: push our documentation to git, in order to have collaborative edition and versioning.
	- Issues:
		- SR Linux configuration documentation does not match the device configuration for the same version. Because of this the IS-IS template was aborted.
		- SR Linux and SROS have different configuration templates
		- No SROS license to continue development
	- Tasks:
		1. Push all ISIS info from asr9k LLD to the repo
		2. Make corrections to SROS one, like adding the net parameter (blocked by issue)
- [ippal-emulated-labs](https://github.com/nosportugal/ippal-emulated-lab)
	- Goal: have a series of lab environments statically coded that can be quickly turned on and used by our teammates.
	- Issues:
		- Currently xrd (control) does not support lacp. It can be configured but it does not work operationally
		- xrd startup config seems to be troubling. I think it erases the base configuration (e.g. ssh)
	- Tasks:
		1. Create a sros only clab blank scenario
		2. Make clab deletecfg action for reusability of a VM-based scenario
		3. Integrate SROS in the vm scenario
		4. Integrate SONIC vm and container router in clab
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
	- Network CI/CD pipedream:
		1. Launch the containerlab pretended scenario
		2. Check using "sudo docker logs <\container-name>" if every router of the scenario has finished booting up
		3. Provision the lab configurations using clabprovision
		4. Run a suite of golang tests to confirm if the configs are operational. The first check must ensure the isis/ldp has stabilized
		5. Run some robot framework tests for cli testing
		6. Return some sort of test summary result
- Future Goals:
	- Understand how to make a lab scenario for integration testing with grafana, collectors, etc...