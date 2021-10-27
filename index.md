## Installation Process

To install my Arch Linux system, the [Arch Wiki Installation Guide](https://wiki.archlinux.org/title/Installation_guide) was used.

An Arch Linux .iso file was received via flash drive to load the .iso into VMWare Fusion. From here, the installation guide follows through the steps to setup a Linux system.

### Installing Arch Linux

First, the boot mode was verified to be UEFI with the command:
```markdown
ls /sys/firmware/efi/efivars
```
Successful running of this command verifies that the system is in UEFI mode.

Next, internet connection was checked with the commands:
```markdown
ip link
ping archlinux.org
```
The network interfaces are listed and packets were returned, confirming internet connection.

## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/jarowmyers/jarowmyers.github.io/edit/main/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/jarowmyers/jarowmyers.github.io/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
