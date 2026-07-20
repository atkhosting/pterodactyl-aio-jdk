
![license mit](https://img.shields.io/badge/license-MIT-green) 
[![build status](https://github.com/AlexAn75541/pterodactyl-aio-jdk/actions/workflows/docker-image.yml/badge.svg?branch=master)](https://github.com/AlexAn75541/pterodactyl-aio-jdk/actions/workflows/docker-image.yml)

# Pterodactyl Multi-JDK Images

A collection of Docker images for the Pterodactyl Panel, providing multiple JDK vendors in a single container. You can switch between vendors at runtime using the `JDK_VENDOR` environment variable. x86 only for now.

## Available Images

| Java Version | Image Tag | Included JDK Vendors |
|--------------|-----------|---------------------|
| 7 | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-7` | Zulu |
| **8*** | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-8` | Temurin, **GraalVM (only `ce` variant)**, Zulu, Corretto, Liberica |
| 9 | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-9` | Zulu, Liberica |
| 10 | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-10` | Zulu, Liberica |
| **11*** | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-11` | Temurin, **GraalVM (only `ce` variant)**, Zulu, Corretto, Liberica |
| 12 | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-12` | Zulu, Liberica |
| 13 | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-13` | Zulu, Liberica |
| 14 | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-14` | Zulu, Liberica |
| 15 | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-15` | Zulu, Liberica, Corretto |
| 16 | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-16` | Temurin, Zulu, Corretto, Liberica |
| **17*** | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-17` | Temurin, **GraalVM (all 3 variants)**, Zulu, Corretto, Liberica |
| 18 | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-18` | Temurin, Zulu, Corretto, Liberica |
| 19 | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-19` | Temurin, Zulu, Corretto, Liberica |
| **21*** | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-21` | Temurin, **GraalVM (all 3 variants)**, Zulu, Corretto, Liberica |
| 24 | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-24` | Temurin, **GraalVM (all 3 variants)**, Zulu, Corretto, Liberica |
| **25*** | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-25` | Temurin, **GraalVM (all 3 variants)**, Zulu, Corretto, Liberica |
| 26 | `ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-26` | Temurin, Zulu, Corretto, Liberica |

> [!NOTE]
> The asterrisk(*) mark in this table here shows the corresponding LTS releases of JDKs, It's highly recommended to use these versions as these get improvements and security updates over extended periods


## Main Features

**Multiple JDK Vendors:**
- **Temurin**: Eclipse Adoptium OpenJDK, a standard and widely trusted build.
- **GraalVM**: Oracle's high-performance JDK. Available as `graalvm`, `graalvm-ce` (Community Edition), and `graalvm-native` (with Native Image).
- ~~Shenandoah: An ultra-low pause time garbage collector~~ REMOVED
- **Zulu**: Azul's certified OpenJDK build.
- **Corretto**: Amazon's production-ready OpenJDK.
- ~~Semeru: IBM's OpenJ9-based runtime.~~ Only available Docker image JVM type is OpenJDK, therefore deemed as unnessesary and removed
- **Liberica**: BellSoft's complete OpenJDK distribution.
- ~~Dragonwell: Alibaba's optimized OpenJDK for production workloads.~~ REMOVED

> [!CAUTION]
> Not all JDK vendors are available for every Java version. Please check the "Included JDK Vendors" table for details.\
> Since almost all OpenJDK builds are more or less the same in term of performance, I will remove some for the sake of the images's size

**Optimizations and other features:**
- Includes the full JDK (not just the JRE) with tools like `javac`, `jshell`, and `jar`(ofc `java` is alway remains included in the image lol). 
- Documentation, samples, and demos are stripped to reduce image size.
- **jemalloc and mimalloc support**: memory allocator with built-in profiling for detecting native memory leaks(except mimalloc), both are compiled from source and included in all images but are disabled by default. *That one Automatic Jemalloc/Jeprof dumps still roughly implemented, will fix later*
- Monthly Github Action build at the start of the first day in a month at UTC timezone, to ensure changes from the upstream images can be automatically added to the next build
- If theres any important patches/updates that needed to be include in these images, feel free to send me a **Build Request** email to `agroup168@proton.me` and I will consider to trigger the building process manually
## How to Use


### Using the `JDK_VENDOR` Environment Variable

To select a JDK vendor, set the `JDK_VENDOR` environment variable in your Pterodactyl Panel.

> [!NOTE]
> If this variable is not set, the image will default to Temurin for maximum compatibility.\
> To use a specific vendor outside the panel, you can set the variable with Docker's `-e` flag:\
> `docker run -e JDK_VENDOR=zulu ghcr.io/alexan75541/pterodactyl-aio-jdk:aio-21`

1. Go to your Pterodactyl Admin Panel → Nests → {Your Chosen Egg}.
2. Navigate to the **Variables** tab and create a new variable.
3. Set the **Environment Variable** to `JDK_VENDOR`.
4. You can allow users to select from the following options: `temurin` (default), `graalvm`, `graalvm-ce`, `graalvm-native`, `zulu`, `corretto`, `liberica`.
5. Save the variable, then restart your server for the changes to take effect.

Alternatively, you can add the following configuration to your egg's JSON file:

```
{
            "name": "JDK Vendor",
            "description": "The JDK vendor to use for running the server.\r\n\r\nOptions: `temurin`, `graalvm`, `graalvm-native`, `graalvm-ce`, `zulu`, `liberica`, `corretto`",
            "env_variable": "JDK_VENDOR",
            "default_value": "temurin",
            "user_viewable": true,
            "user_editable": true,
            "rules": "required|string|in:temurin,graalvm,graalvm-native,graalvm-ce,zulu,liberica,corretto,",
            "field_type": "text"
}

```

Along with the list of Java version if you want:

```
"docker_images": {
        "Java 7": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-7",
        "Java 8": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-8",
        "Java 9": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-9",
        "Java 10": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-10",
        "Java 11": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-11",
        "Java 12": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-12",
        "Java 13": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-13",
        "Java 14": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-14",
        "Java 15": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-15",
        "Java 16": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-16",
        "Java 17": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-17",
        "Java 18": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-18",
        "Java 19": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-19",
        "Java 21": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-21",
        "Java 24": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-24",
        "Java 25": "ghcr.io\/alexan75541\/pterodactyl-aio-jdk:aio-25"
}
```

## malloc stuff (jemalloc / mimalloc)

All images include pre-compiled `jemalloc` and `mimalloc` libraries(long ahh build time), which can improve performance by optimizing memory allocation(Thanks to [Skullians's Repo](https://github.com/Skullians/native-leak-profiling)). Both are disabled by default with its associated `.so` library files and can be enabled with a startup flag.

> [!IMPORTANT]
> You can only enable one memory allocator at a time.

> [!WARNING]
> And as Skullian stated, images with `mimalloc` are experimental, proceed with cautions.

### Enabling a `*malloc`

You can enable them by setting the `MALLOC_IMPL` variable in the egg or by using one of these flags(behind the `java ` start point):
```
-Djemalloc=true

       or 

-Dmimalloc=true

       or

-Dtcmalloc=true
```
`MALLOC_IMPL=none` leaves the default allocator in place. Only one allocator can be active at a time, and `-Ddump=true` requires `jemalloc`.
### The rest of the profiling procedure for `jemalloc` are in [this part](https://github.com/Skullians/native-leak-profiling/blob/main/README.md#usage) of his repo, as well as other knowledges. Be sure to check it out if you're interested.

## License and Contributing

**MIT License - See LICENSE file**

By using Oracle's registry in this repository, I, Aretzera(AlexAn75541) or maybe users of this repository, comply to [GraalVM Free Terms and Conditions (GFTC)](https://www.oracle.com/downloads/licenses/graal-free-license.html).

Moreover, this project includes the following JDK distributions:
- **Eclipse Temurin**: GPLv2 
- **Azul Zulu**: GPLv2   
- **BellSoft Liberica**: GPLv2  
- **GraalVM Community Edition**: GPLv2
- *(And Other vendors...)*

All JDK distributions are used in accordance with their respective licenses.

### Issues and PRs welcome! This repo is maintained for personal use but shared with the community(if they care lol).

**Credits:**

- Pterodactyl Panel: https://pterodactyl.io/
- [RikoDEV's GraalVM Pterodactyl Docker Image Repo](https://github.com/RikoDEV/pterodactyl-graalvm)
- [THE OG YOLK](https://github.com/pterodactyl/yolks)
- [trenutoo's Pterodactyl Docker Image Repo](https://github.com/trenutoo/pterodactyl-images/)
- [native-leak-profiling from Skullians](https://github.com/Skullians/native-leak-profiling)
- [WhichJDK](https://whichjdk.com/) for JDK recommendations
- And all JDK vendors for their amazing work so far and I hope that I won't get killed by them


Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.
