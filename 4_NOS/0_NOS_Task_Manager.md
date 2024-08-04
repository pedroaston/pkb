
This task list tries to both document the future work and also prioritize it.

1. IP Controller
	1. Continue developing slides
2. Kentik Platform
	1. Onboarding the UDP_RELAY Solution (ongoing - wait for DC team)
	2. Introduce Kentik to the B2B team using Thousand Eyes (pending Marco/Filipe authorization)
	3. 1-1k sampling rate on border routers (on-hold due to no licensing)
	4. Flow monitoring the NOS TV CDN solution (on-hold due to no licensing)
3. New IP Engineering Way of Work
	1. Project Details in [[1_New_IP_Eng_Ways]]
	2. Have some prototype ready before mid September
4. UDP Relay
	1. Before deploying to production, repeat the functional tests with gobgp v3.28.0
		1. Just erase the compiled code directories and paste the extract compilation back
		2. Compile a upgarde instructoon tutorial bash script
	2. Have source subscription be made also possible towards subnets instead of single IPs
	3. Work on a dual-homed solution
	4. Test again with FRR
	5. Add BFD
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