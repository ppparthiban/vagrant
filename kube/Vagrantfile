# Variables
node_count = 3
vm_image = "bento/centos-7.4"

Vagrant.configure("2") do |config|
    (1..node_count).each do |node_id|
        config.vm.define "kube#{node_id}" do |kube|
            kube.vm.box = vm_image
            kube.vm.hostname = "kube#{node_id}"
            if node_id < 10 then
                kube.vm.network "private_network", ip: "192.168.10.10#{node_id}"
            else
                kube.vm.network "private_network", ip: "192.168.10.1#{node_id}"
            end
            kube.vm.provider :virtualbox do |vb|
                vb.customize ["modifyvm", :id, "--memory", "2048"]
                vb.customize ["modifyvm", :id, "--cpus", "2"]
            end
            kube.vm.provision "shell", inline: <<-SHELL
                sed -i 's/SELINUX=permissive/SELINUX=disabled/1' /etc/selinux/config
                sed -i 's/SELINUX=enforcing/SELINUX=disabled/1' /etc/selinux/config
                systemctl disable firewalld
                sed -i 's/#PermitRootLogin/PermitRootLogin/1' /etc/ssh/sshd_config
                echo "root" | passwd --stdin root
                yum install -y java-1.8.0-openjdk-devel.x86_64 java-1.8.0-openjdk
                cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
EOF
            SHELL
        end
    end
end
