### Greetings

Hello everyone. I'm happy to be here with you.

I am a DevOps at Serokell company. We do functional programming/consulting and actively participating in the community.

Lets dive into it.

### Introduction to presentation

Nix is great.
* Single stack of technologies to rull them all.
* Cetral community driven repository that consolidates efforts of us all.
* Nix language is very powerfull and flexible.
* Very strict packaging rules. So we sort-out many maintanance points by design and process. It all gust needs polish and polish with a lot of contributors.
* Reproducibility.
* Rollbacks.
and more.

Maybe someone can say two more things important or cool to him.

Let's go to enother level

### Stories:

We know about lerning barrier to become proficient in Nix world. Evetything, approaches are new.

#### Let's consider stories

1. Linux poweruser considers to get feet wet and look into Nix world. He goes and sees there is whole stack. He goes to reddit/his dirtro wiki/forums and reads there that to get a taste - he can use Nix on any distribution.

2. Linux Enterprice professional maintaines complex setups on Mainfraimes. And he heard that Nix can solve his dependensy hell problems, give him stable groud, reprodicibility and rollbacks of configuration and state. 

They both not in hell would suddenly install nuke their systems and pave NixOS on it.

They both going to fire-up Nix on their-own systems first first, to have some understanding what they get.

### Let's do live installation of Nix on Ubuntu base, Arch Linux and Alpine.

As a person who spent quite a time on Nix installator and how Nix works on system, and as also I know Linux great. - Nix can and should install and work on any Linux distribution, and BSD one. Nix uses just two things in environment during installation. That are PATH&HOME, and root access to create /nix. And /nix directory is solely Nix private property.

Imagine. In any Linux destribution, BSD system and looks like Windows can be figured-out also Windows is POSIX 1.1 complaint so it supports most of bare bone things that Nix uses.

But let's go look at sharpness of reality.

### Lets deploy it!

I going to do it in containers.
Docker conteiners are closer to server environments, than Desktop with whole convinient setup for user and whole GUI stack.
Docker containers are strict to the point.

And BTW, Docker is the dominant deployment platform in itself. So Nix deployment should be able to work in Docker.

Case: Startup have whole infrastructure in one huge container: 2-3 GBs image snapshot is reallity. "Use Nix container" does not applies here. They would need to install Nix in that beast container.

### Docker demo

I would split my personality. First one is a Linux user. Second is a Linux professional that going to know cause of issues in advance and explain to while does things.

##### Prepare

```
docker run -it ubuntu:16.04 /bin/bash
```

Release version:


```
echo "$(tput setaf 2)$(grep PRETTY_NAME /etc/os-release | cut -d= -f2)$(tput sgr0)""Ubuntu 16.04.3 LTS"
```

I've taken the most popular and polished Docker image - current Ubuntu LTS.

##### Dependency

```
apt update && apt -y install curl bzip2

curl https://nixos.org/nix/install -o install.sh
chmod u+x ./install.sh
./install.sh
```


##### User

Error...

USER not set.

Describing. Solving

```
export USER=root
```


##### Sudo


WARNING on root.

Please! Please!! Someone!, - explain to me why that is not possible and why that disclaimer is there.

This disclaimer is real hypocricy. Because one second later Nix runs to us begging to give him root access, to be able to do work on the system.

If you look into dictionary under "hypocricy" - example of this disclamer is there. I've seen it in one already.

#####

sudo not present.

God damn. It is lightweight system. It is microcontroller in a plain. If su is barebone, has smallest attack surface, and all noninteractive features we need - probably adding a sudo of caurce is a plesent convinient present to our hacker. I know, he uses sudoless on our controllers.

```
apt install sudo
```


##### Nixbld group and users

... Error...

In my Linux setup group `nixbld`` is not present. How it can be that Red Hat, Debian and Gentoo does not have this group also?

Nix just complains and quits.

Nix uses girlfriend tactic here. Nix must created group and worker users for herself.

So she decides to imagines a situation in which I am guilty that she was unable to wear that dress on a Opera night, because I not read her mind and not bought that dress she was not sure about yesterday.

You know that even Great Gutsby (Image of Gutsby Dicaprio throwing dresses) throwing dresses at Nix would not guess that Nix said that it does single user install and so no workers, but wants that NixBLD dress, with that colorfull worker nixbld# users everywhere.

At start of script installation says: echo "performing a single-user installation of Nix..."

```
groupadd -r nixbld
for n in $(seq 1 10); do useradd -c "Nix build user $n" \
    -d /var/empty -g nixbld -G nixbld -M -N -r -s "$(which nologin)" \
    nixbld$n; done
```

##### Shell profile

Make both changes to shell initialization and current interactive shell.

Only after this fix Nix installation can go into using Nix after install.

### My updated version

```
docker run -it ubuntu:16.04 /bin/bash
```

```
apt update && apt -y install git curl bzip2

git clone -b installFullProgress https://github.com/Anton-Latukha/nix.git
```

Old notes.
### Irreplacability

Nix/NixPkgs/NixOS/NixOps - the same can be acomplished with Ansible + Terraform.
Except for pure immutable Nix derivations model.

But there is now in Linux world Snap packages from Canonical. And they put a lot of manpower to it, and a lot of hype around it.
Several major distributions already adopted Snaps as their thrird-party package model. Fedora dopted it also with their Flatpacks and Solus already started using Snaps as third-party universal package format.

Snap started as a container concept, but a lot of funding and work goes into it. So in Snap get retrofitted many major possibilities, like clojures, and functionality keep approaching Nix possibilities.

Canonical with slapping Ubuntu filespace on every other distribution.

And when Snaps get adopted by proprietary companies for software distribution.
Distributions would not have other option but to adopt Snaps, and put Canonical filespace inside own filespace.

And Canonical board probably knows that going to create a lockin of everyone on Ubuntu. And all proprietary software companies going to pay Canonical for support and packaging/testing of Snaps.

If Linux installation of Nix was snappy, or more known, probably snap never been an thing. In all this discussions I heard over the last two years in Linux world, I heads people mentioning Nix only two times. They heard things but had no clue about, so discussions about Nix stopped there.

On the contrary, knowledgeble in Nix* people would say - "It solves all that problems, everything". Difference and wall between what people in Linux hear and know and our 1% -  that is why that answer get not paid attention to. It seems ridicilous and bafoonish from Linux user perspective.

### NixPkgs is something never seen

Because of Nix laziness - all packages are in the same repository, so work with them, develop them, use them is much more convinient.
It is completely portable to any Linux. But closures are filespace in itself.

### Strength of Nix*

Weakness of Nix* concurents - is segmentation and fragmentation.
* Snap/Docker containers can be based on Ubuntu, Fedora, Debian, CentOS, Alpine, Arch Linux, or be build from scratch.
* And Snap still internally in part relies on package managers, so apt, apt-get, dnf, yum, pacman, apk is still used within Snaps/Docker containers
* Confiruration management is fragmented: Puppet, Cheff, Salt Stack, Ansible. Any other job requires Cheff, some Puppet, Ansible.
* Terraform vs CloudFormation vs Heat is still also true. I interviewed in many companies that use CloudFormation.

