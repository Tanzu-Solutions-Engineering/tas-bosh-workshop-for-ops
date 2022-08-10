
- `bosh vms` - to list all VMs across their foundation 
- `bosh instances —ps` - to list processes that may be failing on the VM

SSH into each of the failing VMs to attempt to restart all required processes.   
- `bosh ssh <VM-Type>/<VM-GUID>  -d <cf-DeploymentName>`

Once logged in the SRE team member can su to root and then run monit summary to list all non-running processes that are required for TAS 
- `monit summary`

Now the SRE team member can start all monit processes that are stopped or start them individually 
- `monit start all`