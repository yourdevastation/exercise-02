Vagrant.configure('2') do |config|
  config.vm.define 'app' do |app|
    app.vm.box = 'ubuntu/jammy64'
    app.vm.box_version = '20241002.0.0'
    app.vm.hostname = 'app'
    app.vm.network 'public_network', ip: '192.168.0.3'
    app.vm.synced_folder '.', '/vagrant', disabled: true

    app.vm.provider 'virtualbox' do |vb|
      vb.memory = 4096
      vb.cpus = 4
      vb.name = 'app_ubuntu'
    end
  end

end