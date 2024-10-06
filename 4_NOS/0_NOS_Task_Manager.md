
This task list tries to both document the future work and also prioritize it.

1. IP Controller
	1. Wait for Ops final proposal
2. Kentik Platform
	1. Start migrating the CDN flows to the UDP RELAY (STANDBY: CRQ committed and waiting)
	2. Talk with Manolis and Ketty about Synthetics testing (twitter example)
		1. Ask via teams to David if there is interest from their side.
	3. 1-1k sampling rate on border routers (on-hold due to no licensing)
	4. Flow monitoring the NOS TV CDN solution (on-hold due to no licensing)
3. New IP Engineering Way of Work
	1. Project Details in [[1_New_IP_Eng_Ways]]
4. UDP Relay
	1. Submit CRQ 
	2. Backlog
		1. Compile a upgrade instruction tutorial bash script
		2. Have source subscription be made also possible towards subnets instead of single IPs
		3. Add BFD for BGP
5. Peering LLD
	1. Document and propose a solution for Cogent failure, and TATA loadbalance
	2. Update contents of the Peering LLD
	3. 1st Rollout meeting (on-hold until September due to Gonçalo Unavailability)
6. Telemetry Engine
	1. Integrate with containerLab environment
	2. ygot integration
	3. grpcTLS option
	4. BGP models for SROS
	5. Normalization Metrics Layer