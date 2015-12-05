# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = '2'

Vagrant.require_version '>= 1.5.0'

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  config.vm.hostname = 'depot.local'

  # Set the version of chef to install using the vagrant-omnibus plugin
  # NOTE: You will need to install the vagrant-omnibus plugin:
  #
  #   $ vagrant plugin install vagrant-omnibus
  #
  if Vagrant.has_plugin?("vagrant-omnibus")
    config.omnibus.chef_version = 'latest'
  end

  # Every Vagrant virtual environment requires a box to build off of.
  # If this value is a shorthand to a box in Vagrant Cloud then
  # config.vm.box_url doesn't need to be specified.
  config.vm.box = 'threatstack/ubuntu-14.04-amd64' #only chef installed
  # config.vm.box = 'phusion/ubuntu-14.04-amd64' #only docker installed
  # config.vm.box = 'williamyeh/ubuntu-trusty64-docker' #only docker installed
  # config.vm.box = 'tkak/ubuntu-14.04-amd64-chef-dk' #docker1.5 and chef installed


  # Assign this VM to a host-only network IP, allowing you to access it
  # via the IP. Host-only networks can talk to the host machine as well as
  # any other machines on the same network, but cannot be accessed (through this
  # network interface) by any external networks.
  config.vm.network :private_network, type: 'dhcp'
  config.vm.network "forwarded_port", guest: 50000, host: 5000, auto_correct: true #manager
  config.vm.network "forwarded_port", guest: 8080, host: 8080, auto_correct: true #load balancer

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider :virtualbox do |vb|
    # Don't boot with headless mode
    vb.gui = true

    # Use VBoxManage to customize the VM. For example to change memory:
    vb.customize ["modifyvm", :id, "--memory", "2048"]
  end
  #
  # View the documentation for the provider you're using for more
  # information on available options.

  # The path to the Berksfile to use with Vagrant Berkshelf
  # config.berkshelf.berksfile_path = "./Berksfile"

  # Enabling the Berkshelf plugin. To enable this globally, add this configuration
  # option to your ~/.vagrant.d/Vagrantfile file
  config.berkshelf.enabled = true

  # An array of symbols representing groups of cookbook described in the Vagrantfile
  # to exclusively install and copy to Vagrant's shelf.
  # config.berkshelf.only = []

  # An array of symbols representing groups of cookbook described in the Vagrantfile
  # to skip installing and copying to Vagrant's shelf.
  # config.berkshelf.except = []

  config.vm.provision 'chef_zero' do |chef|
    # Specify the local paths where Chef data is stored
    # chef.cookbooks_path = "cookbooks"
    chef.environments_path = 'environments'
    chef.environment = 'example'

    # Add a recipe
    chef.add_recipe 'depot'
    chef.json = {
        'vagrant' => {
            'install_desktop' => false
        },
        'greyhole' => {
            'allow_multiple_sp_per_device' => true #for testing, all greyhole drives are on the same physical drive
        },
        'manager' => {
            'load_balancer' => {
                'listen_port' => '8080' #for testing with a vagrant box, override the listen port so we can test with hostfile changes
            }
        }
    }
  end

end
