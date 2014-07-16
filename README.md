# mac-dev-box
I want to automate my Mac OS X Mavericks setup, because you never know when [it might crash](https://www.youtube.com/watch?v=x_ppg054OR0).

So I try my automation script in a Vagrant environment. I'm using just a simple shell provider for these reasons:

* learn how to automate some steps (like install a .dmg file)
* have a provision script that could be loaded from HTTP and executed on a real MBP.

But before you ask, I also know [BOXEN](https://boxen.github.com):

    QUIT WORRYING ABOUT YOUR TOOLS.
    Automate the pain out of your development environment. Boxen installs your dependencies so you can focus on getting things done.

But they also have to do the Xcode Command Line Tool download in a [semi automated way](https://github.com/boxen/boxen-web/commit/b26abd0d681129eba0b5f46ed43110d873d8fdc2).

Another thing I have learned so far is [caskroom.io](https://github.com/caskroom/)

    "To install, drag this icon..." no more!

Here you find many more packages to install from command line.

## Shopping
To build a mac-dev-box on your MBP, you will need the following:

* [MacBook Pro](http://store.apple.com/de/buy-mac/macbook-pro) if you haven't one already
* [Packer 0.6.0](http://www.packer.io/downloads.html)
* [Vagrant 1.6.3](http://www.vagrantup.com/downloads.html)
* [VMware Fusion 6](http://www.vmware.com/de/products/fusion/), 54 €
* [vagrant-vmware-fusion](http://www.vagrantup.com/vmware#buy-now) plugin, 59 €

## The basebox
You have to build an OS X Mavericks basebox from [box-cutter/osx-vm](https://github.com/box-cutter/osx-vm).

I already have a prepared dmg file. Please read the [box-cutter docs](https://github.com/box-cutter/osx-vm/blob/master/README-timsutton.md) for details.
These were my steps to build the osx109 basebox.

```
git clone https://github.com/box-cutter/osx-vm
cd osx-vm
mkdir -p "iso/OS X Mavericks"
touch "iso/OS X Mavericks/Install OS X Mavericks.app"
mkdir -p dmg
cp /your/prepared/osx.dmg dmg/OSX_InstallESD_10.9_13A603.dmg
touch dmg/OSX_InstallESD_10.9_13A603.dmg
make vmware/osx109
vagrant box add osx109 box/vmware/osx109-nocm.box
```

## Xcode Developer Tools
You have to download the Xcode Command Line Tools manually, because the direct link responds only with HTTP 403 forbidden. So, download Xcode Command Line Tools from [Downloads for Apple Developers](http://developer.apple.com/downloads/) or use the `xcode-select --install` command if you don't have an Apple Developer ID.

These are my current steps to build the mac-dev-box with the pre-downloaded dmg file:

```
git clone https://github.com/StefanScherer/mac-dev-box
cd mac-dev-box
mkdir -p resources
cp /your/already/downloaded/commandline.dmg resources/command_line_tools_for_osx_mavericks_april_2014.dmg
vagrant up
```

## Time saver
To speed up the development of such a provision script, I have switched from the 
[vagrant-vbox-snapshot](https://github.com/dergachev/vagrant-vbox-snapshot) plugin to [vagrant-multiprovider-snap](https://github.com/scalefactory/vagrant-multiprovider-snap).

```
vagrant plugin uninstall vagrant-vbox-snapshot
vagrant plugin install vagrant-multiprovider-snap
```

So you build a clean machine with this and take a snapshot:

```
vagrant up --no-provision
vagrant snap take
```

Then you can do what you want with the box, manually or with vagrant provision scripts. After your provision script has improved enough, test it again with:

```
vagrant snap rollback
vagrant provision
```

# Licensing
Copyright (c) 2014 Stefan Scherer

MIT License, see LICENSE for more details.