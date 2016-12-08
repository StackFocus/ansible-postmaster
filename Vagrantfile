Vagrant.configure(2) do |config|
  config.vm.box = "boxcutter/ubuntu1604"
  config.vm.network "forwarded_port", guest: 8080, host: 8080
  config.vm.synced_folder "./", "/opt/ansible-postmaster"

  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "tests/vagrant.yml"
    ansible.galaxy_role_file = "tests/requirements.yml"
    ansible.sudo = true
  end
end
