tor-browser-crawler
===============

This repository contains the source code for the data collection part of our ACM CCS’14 paper [“A Critical Analysis of Website Fingerprinting Attacks”](http://homes.esat.kuleuven.be/~mjuarezm/index_files/pdf/ccs14.pdf) [1].

We used [Selenium](https://selenium-python.readthedocs.org/) to drive the **Tor Browser** and [stem](https://stem.torproject.org/) to control the tor process. Our implementation is loosely based on [tor-browser-selenium by  @isislovecruft](https://github.com/isislovecruft/tor-browser-selenium). 

The crawler follows the structure described by Wang and Goldberg in their ACM WPES’13 paper. We also used their notation and thus we refer to their paper for the definitions of `batch` and `instance` [2].

We include a number of tests that verify the basic functionality of the code, but we note that it isn’t a complete test suite. In order to test that the *httpseverywhere* extension is correctly installed in the Tor browser spawn by our driver, we followed the same logic as in the [Selenium tests of the *httpseverywhere* project](https://gitweb.torproject.org/boklm/tor-browser-bundle-testsuite.git/tree/mozmill-tests/tbb-tests/https-everywhere.js ).

Requirements
---------------
* Required Linux packages: ```python tcpdump wireshark Xvfb```
* Required python packages: ```selenium requests stem psutil xvfbwrapper```


# Getting started

### 1. Configure

* We recommend running crawls in a VM or a container (e.g. LXC) to avoid perturbations introduced by the background network traffic and system level network settings. Please note that the crawler will not only store the Tor traffic but will capture all the network traffic generated during a visit to a website. That’s why it’s extremely important to disable all the automatic/background network traffic such as the auto-updates. See, for example the [instructions for disabling automatic connections for Ubuntu](https://help.ubuntu.com/community/AutomaticConnections).

* You’ll need to set capture capabilities to your user: `sudo setcap 'CAP_NET_RAW+eip CAP_NET_ADMIN+eip' /usr/bin/dumpcap`

* You might want to change the MTU of your network interface and disable NIC offloads that might make the traffic collected by tcpdump look different from how it would have been seen on the wire.

 * Change MTU to standard ethernet MTU (1500 bytes): `sudo ifconfig <interface> mtu 1500`

 * Disable offloads: `sudo ethtool -K <interface> tx off rx off tso off gso off`

See wireshark [recomendations](https://wiki.wireshark.org/CaptureSetup/Offloading) for more info.

* [Download the TBB](https://www.torproject.org/download/download.html.en) and extract it to `./tbb/tor-browser-linux<arch>-<version>_<locale>/`.

### 2. Run a crawl with defaults:

```
python main.py -u ./etc/localized-urls-100-top.csv -e wang_and_goldberg
```

To get all the available command line parameters and the usage run:

```
python main.py --help
```

### 3. Check out the results

The results are in the `results` folder in the root directory:

    * Pcaps: `./results/latest`
    * Logs: `./results/latest_crawl_log`


Notes
-------
* Installed and tested in *xubuntu 14.04* and *debian 7.8*.


References
-------------

[1] M. Juarez, S. Afroz, G. Acar, C. Diaz, R. Greenstadt, “A Critical Analysis of Website Fingerprinting Attacks”, in the proceedings of the ACM Conference on Computer and Communications Security (CCS), pp. 263-274, ACM, 2014.

[2] T. Wang and I. Goldberg. “Improved Website Fingerprinting on Tor”, in the proceedings of the ACM Workshop on Privacy in the Electronic Society (WPES), pp. 201–212. ACM, 2013.
