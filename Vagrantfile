required_plugins = %w( vagrant-hostsupdater vagrant-berkshelf )

required_plugins.each do |plugin|
  exec "vagrant plugin install #{plugin};vagrant #{ARGV.join(" ")}" unless Vagrant.has_plugin? plugin || ARGV[0] == 'plugin'
end


Vagrant.configure("2") do |config|
  config.omnibus.chef_version = '14.12.9'
  config.vm.box = "ubuntu/xenial64"
  config.vm.synced_folder "app", "/home/vagrant/app"

  config.vm.provision 'chef_solo' do |chef|
    chef.cookbooks_path = "Cookbooks"
    chef.add_recipe 'jobs_watch'
    chef.nodes_path = 'Cookbooks/jobs_watch/nodes'
  end
end
