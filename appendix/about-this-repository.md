# About This Repository

This repository is the source code of our manual.

## ~~Building~~

~~You need to install `gitbook-cli` first. Install `yarn`~~

[https://classic.yarnpkg.com/en/docs/install\#debian-stable](https://classic.yarnpkg.com/en/docs/install#debian-stable)

~~Install `gitbook-cli` using `yarn`~~

```text
yarn global add gitbook-cli
```

~~When you build this for the first time, you need to install plugins.~~

```text
gitbook install
```

~~Then run~~

```text
gitbook build . docs
```

~~If you want to preview, run the command below. The local website is~~ [http://localhost:4000](http://localhost:4000)~~.~~

```text
gitbook serve
```

## Deploy

The old way to build the project to website using `gitbook-cli` is not working since `gitbook-cli` is not supported any more. Instead, we can simply use [gitbook dashboard](https://app.gitbook.com). You should first have this repository on your GitHub.

* After signing up, create a new space called “NJU computer network lab”.
* Under the tab “integrations”, turn on GitHub and authorize the ‘GitBook’ application on GitHub.
* Import this repository and wait for a few minutes. Here comes the website!

## License

The manual is distributed under a Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License.

## Repository Structure

The body is placed under the `content` folder, and the chapters are placed in the corresponding chapter folder, starting with `ch` and followed by two-digit numbers.

Each section and subsection is placed in the chapter folder. The pictures and other content used in the text are placed in the `assets` folder in the corresponding chapter folder.

