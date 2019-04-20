# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  glusterfs_version = "41"

  # vagrant-cachier requires base-box with VirtualBox Guest Additions installed.
  #config.cache.scope = :box
  #config.cache.enable :yum

  # We setup three nodes to be gluster hosts, and one gluster client to mount the volume
  (1..3).each do |i|
    config.vm.define vm_name = "gluster-server-#{i}" do |config|
      config.vm.hostname = vm_name
      ip = "172.21.12.#{i+10}"
      config.vm.network :private_network, ip: ip
      config.vm.provision :shell, :inline => "yum install centos-release-gluster#{glusterfs_version} -y && yum install glusterfs glusterfs-libs glusterfs-server -y", :privileged => true
      config.vm.provision :shell, :inline => "systemctl enable glusterd.service && systemctl start glusterd.service", :privileged => true
 
      # setup the Gluster volume on gluster-server-3
      if config.vm.hostname == "gluster-server-3"
        config.vm.provision :shell, :inline => "gluster peer probe 172.21.12.11 ; gluster peer probe 172.21.12.13; gluster peer probe 172.21.12.12", :privileged => true
        config.vm.provision :shell, :inline => "gluster volume create glustertest replica 3 transport tcp 172.21.12.11:/brick 172.21.12.12:/brick 172.21.12.13:/brick force", :privileged => true
        config.vm.provision :shell, :inline => "gluster volume start glustertest", :privileged => true
      end

    # config
    end

  # 1..3 loop
  end

  (1..2).each do |i|
    config.vm.define vm_name = "gluster-client-#{i}" do |config|
      config.vm.hostname = vm_name

      # adjust the gluster-client IP assignment for more clients
      ip = "172.21.12.#{i+7}"

      # vagrant-cachier requires base-box with VirtualBox Guest Additions installed.
      #config.cache.scope = :box
      #config.cache.enable :yum

      config.vm.network :private_network, ip: ip
      config.vm.provision :shell, :inline => "yum install glusterfs glusterfs-fuse attr -y", :privileged => true

      # setup the Gluster volume on gluster-client
      config.vm.provision :shell, :inline => "mkdir /mnt/glusterfs && mount -t glusterfs 172.21.12.11:/glustertest /mnt/glusterfs", :privileged => true

    # config
    end

  # 1..2 loop
  end

# Vagrant.configure
end
