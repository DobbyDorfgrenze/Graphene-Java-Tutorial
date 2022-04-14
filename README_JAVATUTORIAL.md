<!-- HEADLINE -->
<p align="center">
  <h3 align="center">A Graphene-Java Tutorial</h3>
  <p align="center">
    A quick start guide for your own java enclaive!
  </p>
</p>



<!-- TABLE OF CONTENTS -->
<details open="open">
  <summary><h2 style="display: inline-block">Table of Contents</h2></summary>
  <ol>
    <li>
      <a href="#about-the-project">About The Project</a>
      <ul>
        <li><a href="#built-with">Built With</a></li>
      </ul>
    </li>
    <li>
      <a href="#getting-started">Getting Started</a>
      <ul>
        <li><a href="#prerequisites">Prerequisites</a></li>
        <li><a href="#installation">Installation</a></li>
      </ul>
    </li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#roadmap">The Manifest</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
  </ol>
</details>

<!-- ABOUT THE PROJECT -->
## About The Project

This is a tutorial for running a java program in an enclaive using graphene.

### Built With

* [Graphene](https://graphene.readthedocs.io/en/latest/oldwiki/Introduction-to-Graphene-SGX.html)
* [Java](https://www.java.com/de/about/whatis_java.jsp)

<!-- GETTING STARTED -->
## Getting Started

To get a local copy up and running follow these simple steps.

### Prerequisites

First update the `apt` package index with:
 ```sh
  sudo apt update
  ```

Then you'll need to have installed git:
  ```sh
  sudo apt install git-all
  ```
You also have to patch your kernel. Here is a [https://www.youtube.com/watch?v=7a8lYEvBqJE] (quick guide) on how to do it.

### Installation

1. Clone the Graphene repo
   ```sh
   git clone https://github.com/oscarlab/graphene.git
   ```
2. Build Graphene
   ```sh
   sudo apt-get install -y build-essential autoconf gawk bison wget python3
   ```
   ```sh
   cd graphene
   ```
   ```sh
   make
   ```
   ```sh
   meson build --buildtype=release -Ddirect=enabled -Dsgx=disabled
   ```
   ```sh
   ninja -C build
   ```
   ```sh
   sudo ninja -C build install
   ```
3. Install Java
   ```sh
   sudo apt-get install openjdk-11-jdk
   ```
4. Make a Directory for your project
   ```sh
   cd graphene
   ```
   ```sh
   cd Examples
   ```
   ```sh
   mkdir java
   ```

<!-- USAGE EXAMPLES -->
## Usage

Now you can write java programs. For example a helloworld script:
   ```sh
   nano HelloWorld.java
   ```
Copy the following code into the file:
   ```
   class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!"); 
    }
   }
   ```
The .class file is made the usual way with:
   ```sh
   javac HelloWorld.java
   ```
Next you have to generate the manifest:
   ```sh
   jar cvfm hello.jar HelloWorld.mf HelloWorld.class
   ```
And build it with:
   ```sh
   make (non-debug) or make DEBUG=1 (debug)
   ```
Now you can run the program in an enclaive:
   ```sh
   graphene-direct java -jar HelloWorld.jar
   ```
If you are using a virtual machine and get an error like "Too small maximum heap", it means the VM used not enough memory for running the script. You can easily fix this with:
   ```sh
   graphene-direct java -Xmx2g -jar HelloWorld.jar
   ```
Xmx2g means, that you are assigning 2 Gigabyte (2g) to the enclaive size.

<!-- MANIFEST -->
## The Manifest

In this part we will go over the manifest file, which needs to be configured right, to run programs in an enclaive.
If you want to change somthing you need to alter the manifest. Open it with
   ```sh
   nano java.manifest
   ```
If you have installed java in another location than the recommended path, you need to change the line
   ```
   libos.entrypoint = "/usr/lib/jvm/java-11-openjdk-amd64/bin/java"
   ```
to wherever you installed java. 
The same goes for Graphene. If you have installed it in another directory, you need to alter the path for the lybsysdb.so:
   ```
   loader.preload = "file:/usr/local/lib/x84_64-linux-gnu/graphene/lybsysdb.so"
   ```
To start your java programm from the command line the following boolean has to be set true:
   ```sh
   loader.insecure__use_cmdline_argv = true
   ```
Otherwise Graphene won´t read arguments comming from the commandline.
Next we have the environment variables. If you want to add a new library, just put a ":" at the end of the string and write down the directory path. It should look like this:
   ```
   loader.env.LD_LIBRARY_PATH = "/lib:{{ arch_libdir }}:/usr/lib:/usr/{{ arch_libdir }}"
   ```
After that we have six blocks where the file systems (FS) are mounted, which should all look like this:
   ```
   fs.mount.lib.type = "chroot"
   fs.mount.lib.path = "/lib"
   fs.mount.lib.uri = "file:/usr/local/lib/84_64-linux-gnu/graphene/runtime/glibc"
   ```
"chroot" mounts a part of the host file system into the guest, so that they can be used.
That means that commands like `javac` can be used in Graphene, because it is usually installed at "/usr/bin/javac", which is mounted automatically. If it is not, you can change the following block:
   ```
   fs.mount.lib.type = "chroot"
   fs.mount.lib.path = "/usr"
   fs.mount.lib.uri = "file:/usr"
   ```

<!-- CONTRIBUTING -->
## Contributing

Now you should be able to run your own java code in an enclaive, so let the Magic happen. Happy Hacking! :)

<!-- LICENSE -->
## License

This Tutorial is free to use for everyone.

<!-- CONTACT -->
## Contact

Julian D. Schreiber - julian.schreiber@stud.hs-flensburg.de - Matr.Nr.: 650043

Project Link : [https://github.com/DobbyDorfgrenze/Graphene-Java-Tutorial](https://github.com/DobbyDorfgrenze/Graphene-Java-Tutorial)
