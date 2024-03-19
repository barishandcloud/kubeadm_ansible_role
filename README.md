# This repository host an ansible playbook that can be used to bootstrap a cluster.

Assumption in this scenario is that we have ubuntu 22.04 vms created in virtualbox that has adapters attached to NAT and Host-only. This way we usually get two IP ranges. Off these one of the ranges commences with 192.168.56.10X. The pre-req for running this playbook is 
* keys generated with the usage of ```ssh-keygen -f filename ```, in this case the filename is udkeys and the private key file path is marked in hosts.ini (present in this repo). 
* The afore mentioned IPs with a bare minimum of two IPs belonging to atleast two vms (to be used as master and worker and named km and kw1 in the hosts.ini). The IP along with hostname entries are made in hosts.ini
* The afore-mentioned IPs with a bare minimum of two IPs belonging to atleast two vms (to be used as master and worker). The IP along with hostname entries are made in hosts.ini
* The user should be part of sudoers in those two vms (i.e. km & kw1) ```echo "$USER ALL=(ALL:ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/$USER ``` in this case, manju and entry marked in hosts.ini

If everything above is done an ad-hoc ping check should yield:
```bash
ansible all -m ping -i hosts.ini --ssh-common-args='-o StrictHostKeyChecking=no'                                                     
kw1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
km | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```
Also to verify if sudoers without passwords working as expected, we can expect:
```bash
ansible all -m shell -i hosts.ini --ssh-common-args='-o StrictHostKeyChecking=no' -a "date" --become
kw1 | CHANGED | rc=0 >>
Tue Mar 19 12:59:57 PM UTC 2024
km | CHANGED | rc=0 >>
Tue Mar 19 12:59:58 PM UTC 2024
```

## Running the playbook

Use the package manager [pip](https://pip.pypa.io/en/stable/) to install foobar.
The main task is invoked by

```bash
ansible-playbook -i hosts.ini main.yaml
```

## Usage

```python
import foobar

# returns 'words'
foobar.pluralize('word')

# returns 'geese'
foobar.pluralize('goose')

# returns 'phenomenon'
foobar.singularize('phenomena')
```

## Contributing

Pull requests are welcome. For major changes, please open an issue first
to discuss what you would like to change.

Please make sure to update tests as appropriate.

## License

[MIT](https://choosealicense.com/licenses/mit/)