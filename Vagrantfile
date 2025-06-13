Vagrant.configure('2') do |config|
  config.vm.define 'app' do |app|
    app.vm.box = 'ubuntu/jammy64'
    app.vm.box_version = '20241002.0.0'
    app.vm.hostname = 'app'
    app.vm.network 'private_network', ip: '192.168.56.3'
    app.vm.network 'forwarded_port', guest: 5432, host: 5432
    app.vm.network 'forwarded_port', guest: 5000, host: 5000
    app.vm.synced_folder '.', '/vagrant', disabled: true

    app.vm.provider 'virtualbox' do |vb|
      vb.memory = 4096
      vb.cpus = 4
      vb.name = 'app_ubuntu'
    end
  end

  config.vm.provision 'ansible' do |ansible|
    ansible.playbook = 'site.yml'
    ansible.inventory_path = 'environments/dev/hosts'
    ansible.vault_password_file = '.vault_password.txt'
  end
end
