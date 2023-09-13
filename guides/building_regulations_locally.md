---
title: Building Regulations Locally
layout: default
parent: Guides
---

This page aims to guide you through the process of building the WCA Regulations and Guidelines, which is something that you may want to do when you first clone the website repository.

## Requirements

{: .warning}
> Chef may be deprecated soon - rather rely on the steps described here.

The requirements to build the Regulations locally are pretty small, and can be found in the chef recipes for the [regulations](https://github.com/thewca/worldcubeassociation.org/blob/a604f5a600dc52943101a00e7a3d2e24c086eecd/chef/site-cookbooks/wca/recipes/regulations.rb), but here is a commented step-by-step setup:

The following system packages are needed:
`git`, `python-pip`, `fonts-unfonts-core`, `fonts-wqy-microhei`, `fonts-ipafont`, `lmodern`

The first two are likely to be already installed on your system, and the last four are needed to build pdfs for the CJK translations of the Regulations.
Please note that the name may differ a bit depending on your Linux distribution.

The html/pdf/json generation is done by the [WCA Regulations Compiler](https://github.com/thewca/wca-regulations-compiler), which can be installed through `pip`:

```
pip install wrc --upgrade
```

To generate pdf, `wrc` relies on a tool named `wkhtmltopdf`, so you need to get it too. We use a precompiled binary that can be found [here](https://wkhtmltopdf.org/downloads.html).
The following two lines will get it for you and install it to `/usr/local`:
```
wget https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.4/wkhtmltox-0.12.4_linux-generic-amd64.tar.xz -O wkhtml.tar.xz
tar -xf wkhtml.tar.xz --strip-components=1 -C /usr/local
```

# How to build

From the root of the repository, just run:
```
./scripts/deploy.sh rebuild_regs
```

The script will detect if anything needs to be (re)built and do it.
On a first run it will build everything that lives under the `/regulations` route on the website, which includes the WCA Regulations and Guidelines, their translations, the regulations history, the scramble programs, and the json used by omnisearch to search in the Regulations.



