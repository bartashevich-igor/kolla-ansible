pipeline {

  agent any

  stages {
    stage('Setup Local Environment') {
      steps {
        echo '--RUNNING LOCAL ENVIORNMENT --'
        sh ''' #!/bin/bash 
        sudo apt-get update
        sudo apt-get install python3-dev libffi-dev gcc libssl-dev docker.io -y
        sudo apt install python3-pip -y
        sudo apt install python3.10-venv -y
        sudo python3 -m venv local
        source local/bin/activate
        ''' 
      }
    }

    stage('INSTALLING PIP') {
      steps {
        echo '--INSTALLING PIP --'
        sh ''' #!/bin/bash 
        pip install -U pip
        ''' 
      }
    }
    stage('INSTALLING Ansible') {
      steps {
        echo '--INSTALLING Ansible --'
        sh ''' #!/bin/bash 
        pip install 'ansible-core>=2.16,<2.17.99'
        ''' 
      }
    }  
    
    stage('INSTALLING Kolla Ansible') {
      steps {
        echo '--INSTALLING Kolla Ansible --'
        sh '''#!/bin/bash 
        pip install git+https://github.com/bartashevich-igor/kolla-ansible
        kolla-ansible install-deps
        ''' 
      }
    } 

    stage('Preparing Infrastructure') {
      steps {
        echo '--Preparing Infrastructure Files Structure --'
        sh ''' #!/bin/bash 
        sudo mkdir -p /etc/kolla
        sudo chown $USER:$USER /etc/kolla
        cp -r /usr/local/share/kolla-ansible/etc/kolla/* /etc/kolla/ 
        cp -r /usr/local/share/kolla-ansible/ansible/inventory/* /etc/kolla/ 
        ''' 
      }
    }

    stage('Secrets Setup') {
      steps {
        echo '--Generating OpenStack Services Secrets --'
        sh '''#!/bin/bash 
            kolla-genpwd -p /etc/kolla/passwords.yml
           ''' 
      }
    }

    stage('Boostrap Servers') {
      steps {
        echo '--Running Ansible Kolla Boostrap Server Script --'
        sh '''#!/bin/bash 
             kolla-ansible -i /etc/kolla/multi_packtpub_prod bootstrap-servers
           ''' 
      }
    }

    stage('Infrastructure Pre-Checks') {
      steps {
        echo '--Running Ansible Kolla Prechecks Script --'
        sh '''#!/bin/bash 
            kolla-ansible -i /etc/kolla/multi_packtpub_prod prechecks
           ''' 
      }
    }

    stage('Deploy Infrastructure') {
      steps {
        echo '--Running Ansible Kolla Prechecks Script --'
        sh '''#!/bin/bash 
        kolla-ansible -i /etc/kolla/multi_packtpub_prod deploy
        ''' 
      }
    }
  }
}
