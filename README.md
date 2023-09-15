# restart_service
*** Attempt to run a task asynchronously ***
By default, Ansible runs in parallel against all the hosts in the pattern you set in the hosts: field of each play. If you want to manage only a few machines at a time, for example during a rolling update, you can define how many hosts Ansible should manage at a single time using the serial keyword.
In the above example, if we had 6 hosts in the group ‘webservers’, Ansible would execute the play completely (both tasks) on 3 of the hosts before moving on to the next 3 hosts.
```
PLAY [webservers] ****************************************

TASK [first task] ****************************************
changed: [web3]
changed: [web2]
changed: [web1]

TASK [second task] ***************************************
changed: [web1]
changed: [web2]
changed: [web3]

PLAY [webservers] ****************************************

TASK [first task] ****************************************
changed: [web4]
changed: [web5]
changed: [web6]

TASK [second task] ***************************************
changed: [web4]
changed: [web5]
changed: [web6]

PLAY RECAP ***********************************************
web1      : ok=2    changed=2    unreachable=0    failed=0
web2      : ok=2    changed=2    unreachable=0    failed=0
web3      : ok=2    changed=2    unreachable=0    failed=0
web4      : ok=2    changed=2    unreachable=0    failed=0
web5      : ok=2    changed=2    unreachable=0    failed=0
web6      : ok=2    changed=2    unreachable=0    failed=0
```
Setting the batch size with serial changes the scope of the Ansible failures to the batch size, not the entire host list. You can use ignore_unreachable or max_fail_percentage to modify this behavior.

You can also specify a percentage with the serial keyword. Ansible applies the percentage to the total number of hosts in a play to determine the number of hosts per pass:
```
---
- name: test play
  hosts: webservers
  serial: "30%"
```
If the number of hosts does not divide equally into the number of passes, the final pass contains the remainder. In this example, if you had 20 hosts in the webservers group, the first batch would contain 6 hosts, the second batch would contain 6 hosts, the third batch would contain 6 hosts, and the last batch would contain 2 hosts.
