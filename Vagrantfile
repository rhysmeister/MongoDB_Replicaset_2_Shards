Vagrant.configure("2") do |config|
  MONGOS_HOSTS=1
  (1..MONGOS_HOSTS).each do |mongos|
    node_name = "mongos-node#{mongos}"
    config.vm.define node_name do |mongos_node|
      mongos_node.vm.box = "centos/7"
      mongos_node.vm.network "private_network", ip: "192.168.43.#{100 + mongos}"
      mongos_node.vm.hostname = node_name
      mongos_node.vm.provider :virtualbox do |vbox|
        vbox.linked_clone = true
        vbox.name = node_name
      end
      if mongos == MONGOS_HOSTS
        mongos_node.vm.provision :ansible do |ansible|
        ansible.limit = "all" # Connect to all machines
        ansible.playbook = "mongos.yaml"
      end
    end
  end
  MONGOD_HOSTS=6
  (1..MONGOD_HOSTS).each do |mongod|
    node_name = "mongod-node#{mongod}"
    config.vm.define node_name do |mongod_node|
      mongod_node.vm.box = "centos/7"
      mongod_node.vm.network "private_network", ip: "192.168.43.#{200 + mongod}"
      mongod_node.vm.hostname = node_name
      mongod_node.vm.provider :virtualbox do |vbox|
        vbox.linked_clone = true
        vbox.name = node_name
      end
      if mongod == MONGOD_HOSTS
        mongod_node.vm.provision :ansible do |ansible|
          ansible.groups = {
            "mongod" => [
                  "mongod-node1",
                  "mongod-node2",
                  "mongod-node3",
                  "mongod-node4",
                  "mongod-node5",
                  "mongod-node6"
                ],
            "rs1" => [
              "mongod-node1",
              "mongod-node2",
              "mongod-node3"
            ],
            "rs2" => [
              "mongod-node4",
              "mongod-node5",
              "mongod-node6"
            ],
            "mongos" => [
              "mongos-node1"
            ],
            "rs1:vars" => {"replicaset" => "rs1"},
            "rs2:vars" => {"replicaset" => "rs2"}
          }
          ansible.limit = "all" # Connect to all machines
          ansible.playbook = "mongodb.yaml"
        end
      end
    end
  end
end
end
