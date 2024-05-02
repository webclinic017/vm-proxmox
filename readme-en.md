Proxmox Virtual Machine Management Script with FastAPI
This script is a FastAPI based application for managing virtual machines (VMs) on Proxmox. It provides a RESTful API for various VM-related operations including cloning, configuration update, health check, and VM listing.

First steps
Facility
Clone this repository to your local machine.
Enter the IPs in the config.json file for automatic use of IPs
Environment Configuration

Copy the example.env file with the following environment variables:
PROXMOX_HOST - Proxmox cluster server address.
PROXMOX_USER - Proxmox API username.
PROXMOX_PASSWORD - Proxmox API password.
PROXMOX_NODE - Proxmox node name.
Dependencies

Install the required Python packages by running:
pip install -r requirements.txt
Use
Run the script using Uvicorn:
 uvicorn main:app --host 0.0.0.0 --port 8000
Access the API at http://127.0.0.1:8000.
API Access Points
Clone a VM: Create a new VM by cloning an existing one.
Access point: /clone_vm
Method: POST
Settings
source_vm_id (required): ID of the source VM to clone.
new_vm_id: (optional): ID of the new VM (automatically generated if not specified).
new_vm_name: (optional): Name of the new VM.
cpu: (optional): Number of CPU cores for the new VM.
ram: (optional): Amount of RAM memory for the new VM.
disk_type: (optional): Disk type for the new VM.
disk_size: (optional): Disk size for the new VM.
bridge: (optional): Network bridge for the new VM.
ipv4: (optional): IPv4 address for the new VM.
gw:(optional) The IPv4 gataway
ipv6: (optional): IPv6 address for the new VM.
gw6: (optional) The IPv6 gataway
start_vm: (optional): Start the new VM after cloning (by default, not started).
application: (optional) add the name of the playbook to be executed
Example JSON request:

{
 "source_vm_id": 12345,
 "new_vm_id": 100,
 "new_vm_name": "NewVM",
 "cpu": 2,
 "ram": 4096,
 "disk_type": "scsi",
 "disk_size": "50G",
 "bridge": "vmbr0",
 "ipv4": "192.168.1.100",
 "gw": "192.168.1.1",
 "ipv6": "2001:db8::1/64",
 "gw6": "2001:db8::1",
 "start_vm": true,
 "application": "nginx mariadb" # To launch the nginx playbook
}
Update the Configuration of a VM (continued)
Update the configuration of an existing VM.

Access point: /update_vm_config
Method: POST
Settings
vm_id (required): ID of the VM to update.
bridge: (optional): Network bridge for the VM.
ipv4: (optional): IPv4 address for the VM.
ipv6: (optional): IPv6 address for the VM.
cpu: (optional): Number of CPU cores for the VM.
ram: (optional): Quantity of RAM memory for the VM.
disk_type: (optional): Disk type for the VM.
disk_size: (optional): Disk size for the VM.
Example JSON request:

{
 "vm_id": 12345,
 "bridge": "vmbr0",
 "ipv4": "192.168.1.100",
 "cpu": 4,
 "ram": 8192
}
Delete a VM (continued)
Delete an existing VM.

Access point: /delete_vm/{vm_id}
Method: DELETE
Settings
vm_id (required): ID of the VM to delete.
Example request: /delete_vm/12345

Check the Status of a Task
Check the status of a task in progress or completed.

Access point: /check_status
Method: GET
Settings
task_id (required): ID of the task to check.
Example request: /check_status/task_id

List of VMs
Get the list of existing VMs.

Access point: /list_vms
Method: GET
Settings
vmid: (optional): ID of a specific VM to obtain. If not specified, the list of all VMs will be returned.
Example request: /list_vms (to obtain the list of all VMs)

Example request: /list_vms/12345 (to get details of VM with ID 12345)

Task Management
The script tracks task status using task IDs.
Tasks are marked as "In Progress" when initiated and as "Completed" when completed.
Task IDs are returned in the response when creating tasks.
Use :
Enter the proxmox information in the .env file
Enter the IPs in the config.json file for automatic use of IPs
git clone https://github.com/micferna/vm-proxmox.git
cd vm-proxmox
cp example.env .env
python3 -m venv venv
source venv/bin/activate
pip3 install -r requirements.txt
uvicorn main:app --reload
Clone a VM
With the minimum
curl -X POST http://127.0.0.1:8000/clone_vm \
 -H "Content-Type: application/json" \
 -d '{"source_vm_id": 100000, "cpu": 8, "ram": 8096, "disk_type": "sata0", "disk_size": "50G", "start_vm": true}'
Delete a VM
s present on the proximox
curl -X GET "http://127.0.0.1:8000/list_vms"
Detailed information on a VM
curl -X GET "http://127.0.0.1:8000/list_vms?vmid=100"
Update a VM
Each parameter is optional for modifications
curl -X POST -H "Content-Type: application/json" -d '{
 "vm_id": 101,
 "cpu": 1,
 "ram": 512
}' http://127.0.0.1:8000/update_vm_config

# Change only disk type and disk size
curl -X POST -H "Content-Type: application/json" -d '{
 "vm_id": 101,
 "disk_type": "sata0",
 "disk_size": "+200G"
}' http://127.0.0.1:8000/update_vm_config
TO REVIEW
# Clone a full param VM
curl -X POST http://127.0.0.1:8000/clone_vm \
 -H "Content-Type: application/json" \
 -d '{"source_vm_id": 100000, "new_vm_id": 101, "new_vm_name": "VMTESTFASTAPI", "cpu": 8, "ram": 8096, "disk_type": "sata0", "disk_size": "50G ", "bridge": "vmbr0", "ipv4": "192.168.1.10/24", "gw": "192.168.1.1", "ipv6": "fd00::10/64", "gw6": " 2001:db8::1", "start_vm": false, "application": "nginx"}'
# Change only IPv4 and IPv6 IP addresses
curl -X POST -H "Content-Type: application/json" -d '{
 "vm_id": 19075,
 "ipv4": "NEW_IP_IPV4",
 "ipv6": "NEW_IP_IPV6"
}' http://127.0.0.1:8000/update_vm_config
curl -X DELETE http://127.0.0.1:8000/delete_vm/IDVM
Check the status of a task
curl -X GET http://127.0.0.1:8000/check_status?task_id=<task_id>
Shows all VMs
