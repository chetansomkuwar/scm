# ansible
codes for practice and demo purpose

##############################################################################################################################################################


1) debuge module

- name: Working With debug module
  hosts: all

  tasks:

    # Module and argument in the same line
    - name: First debug
      debug:
        msg="Hello world"
...............................................................................................................

2) variable with global

- name: all
  hosts: all				
  vars:
    URL: lereladdu.com
  tasks:
  - name: print 1
    debug:
      msg: "{{URL}}"
...............................................................................................................

3) variable with remote location file (rupam.yml is remote location file content in URL: jamlakabhai.in)

- name: playbook
  hosts: all
  vars_files: rupam.yml  
  tasks:								# here we use "vars_files" module --> data take from remote playbook/file.
  - name: print 1
    debug:
     msg: "{{URL}}"
 
  OR

- name: playbook
  hosts: all
  vars:
   URL: lereladdu.com
  vars_files: rupam.yml  
  tasks:
  - name: print 1						   # here we use global variable "vars" module --> data take from same playbook
    debug:
     msg: "{{URL}}"
  # # vars:
  # #  URL: aws.com  
...............................................................................................................

4) variable with local

- name: playbook1 
  hosts: all
  vars:
   URL: global.com
  tasks: 
  - name: print message_1
    debug:
     msg: "{{URL}}"
  vars:
   URL: local.com
...............................................................................................................

5) variable with hosts ( hosts.yml file --> 3.95.190.63 URL=vaibhavmess.in 3.86.166.35 URL=host_file.cloudblitz.in)

- name: Playbook1
  hosts: all
  tasks: 
  - name: print url
    debug:
      msg: "{{URL}}"
...............................................................................................................

6) varibale with shell 

- name: Playbook1
  hosts: all
  tasks: 
  - shell: uptime >> uptime.txt
  - name: print url
    debug:
      msg: "{{ansible_distribution}}"
...............................................................................................................   

*check file in node via ssh & list

7) variable with prompt

- name: Playbook1
  hosts: all
  vars_prompt:
  - name: URL
    prompt: "Write Anything"
    private: no

  tasks: 
  - name: print url
    debug:
      msg: "{{URL}}"
   
*if you want make it hide prompt remove "private: no "
...............................................................................................................

8) variable cli
ansible-playbook var_file.yml -e URL=Aws.in
...............................................................................................................

9) specific information from node server 

example : ansible_user_dir 

- name: Playbook1
  hosts: all
  tasks: 
  
  - name: print ansible user 
    debug:
      msg: "ansible user: {{ansible_user_dir}}"
...............................................................................................................
   
10) check content and it's sub-contents

- name: Playbook1
  hosts: all
  tasks: 

  - name: print ansible user 
    debug:
      msg: "write any msg: {{ansible_lsb.codename}}"
   

*note : ansible_lsb.codename
it pick content from ansible _lsb to content codename. it like 1.11. dot will seperate contents and it's sub-contents.
........................................................................................................................

11) to install service 

- name: Playbook1
  hosts: all
  tasks: 
  - name: installation of service
    become: true
    apt: 
     name: nginx
     state: latest 
........................................................................................................................

12) tags use to identify sp. attributes 

- name: Playbook1
  hosts: all

  tasks: 

  - name: installation of service
    become: true
    apt: 
     name: ftp 
     state: latest 
    tags: ftp_sevice

  - name: print ansible user 
    debug:
      msg: "ansible user: {{ansible_lsb.codename}}"
    tags: only_print

  - name: print ansible user 
    debug:
      msg: "ansible user: {{ansible_lsb.codename}}"
    tags: only_two


example: hit on terminal---   ansible-playbook akshay.yml -t only two
........................................................................................................................

13) gather facts

 if 
a) gather_facts: yes ------ will work
b) gather_facts: no ------ will not work

- name: playbook_2
  hosts: all
  gather_facts: yes
  tasks:

    - name: Print the task_0
      debug:
        msg: "{{ansible_selinux_python_present}}"
........................................................................................................................

14) ignore errors --- if anone script will not running / for exception put ignore_errors: yes

- name: playbook_3
  hosts: all
  tasks:

    - name: get output
      shell:  ps -elf | grep sleep | grep - v grep
      ignore_errors: yes

    -name: print massage
      debug:
        msg: "hello world"
........................................................................................................................

15) root escaltion -------become: true (will work as root)


- name: Playbook1
  hosts: all
  tasks: 

  - name: installation of service
    become: true
    apt: 
     name: ftp 
     state: latest 
    tags: ftp_sevice
........................................................................................................................

16) copy from ansible server to node server

- name: playbook-1
  hosts: all
  become: true
  tasks:

  - name: copy from server to nodes
    copy:
     src: ansible.cfg
     dest: /mnt/new.txt
........................................................................................................................

17) simple template

- name: template module
  hosts: all
  become: true
  tasks:
    - template:
        src: rupam.yml
        dest: /mnt
........................................................................................................................

18) Loop module with variable

- name: template module
  hosts: all
  become: true
  tasks:
   - name: 
     debug: 
      msg: "my city name:{{item}}"

     loop: 
      - nagpur
      - pune
........................................................................................................................

19) Package Installation module using Loop

- name: pkg module
  hosts: all
  become: true
  tasks:

   - name: package installation
     apt  
      name: "{{item}}"
      state: latest
     loop:
        - ssh
	  - nginx
........................................................................................................................

21) template with variable
       
- name: template module
  hosts: all
  become: true
  vars:
    demo: practice  # mention in file by: {{}}
  tasks:
    - template:
       src: rupam.yml
       dest: /mnt

note: create file with rupam.yml 
in file write {{demo}}
........................................................................................................................

22) blockinmodule ---use to  create & write content in file

- name: blockinfile module
  hosts: all
  become: true
  tasks:
    - blockinfile:
        path: /etc/httpd/conf.d/demo.conf
        create: yes
        block: |
          <virtualhost *:80>
          servername all
          documentroot /var/www/html
          </virtualhost>
........................................................................................................................

23) setfacts ----merge two content same time with use of set_fact

- name: setfacts_playbook
  hosts: all
  tasks:
    - name: set fact
      set_fact: URL=["www.google.com","www.igp.com"] city="nagpur"

    - name: print variable
      debug:
        msg: "{{URL}}  {{city}}"
........................................................................................................................

24) lineinfile module ------  This is primarily useful when you want to change a single line in a file only. 
					It will Manage the line in text file, we can add/remove/replace the line in that file 

---
- name: playbook for lineinfileModule
  hosts: all
  become: true
  tasks:

      - name: new task for file adding and it content
        lineinfile: 
          state: present    
          dest: /mnt/thor.txt
          line: hello spider

#Note: firstly we have to create spider.txt file in Node, after that run the playbook, it means line added in already created file.

      - name: new task for file adding and it content
        lineinfile: 
          state: absent    
          dest: /mnt/thor.txt
          line: hello thor
				     
........................................................................................................................

25) Find Module------- it will find "file_type" from perticular location, we have to mentione their path of file

- name: find playbook
  hosts: all
  become: true
  tasks: 
    - name:
      find:
        path: /var/log  #<where to find>
        file_type: file
        pattern: "*" 
      register: out

    - debug:
        msg={{out}}
-------------------------------------------------------
# get details of path files
    - debug:       
        msg={{item.path}} 
      loop: "{{out.files}}"
------------------------------------------------------
# remove details of file stored in register
    - name: remove the files
      file:
        path: "{{item.path}}"
        state: absent
      loop: "{{out.files}}"
........................................................................................................................

26) When condition with "and" ----In"and condition" if any of the 2 given condiion when 1 condition will be false then whole task will be skipped

- name: simple when condition
  hosts: all
  become: true
  tasks:
    - name: print the task
      debug:
       msg: "Distribution name of Linux: {{ansible_distribution}}"
      when: ansible_distribution == "centos" and ansible_distribution == "Ubuntu"
........................................................................................................................

27) When condition with "or" ----In"or condition" if any of the 2 given condiion when 1 condition will be false it will not skip the next task --> next task will runs smoothly

- name: simple when condition
  hosts: all
  become: true
  tasks:
    - name: print the task
      debug:
       msg: "Distribution name of Linux: {{ansible_distribution}}"
      when: ansible_distribution == "centos" or ansible_distribution == "Ubuntu"

........................................................................................................................

