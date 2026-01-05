# IBM ACE Development with GitHub Copilot

This repository is a demo repository used to show GitHub Copilot's usage on IBM App Connect Enterprise (ACE) and ESQL development.
It features sample ACE code from a repo by IBM Tech Xchange Lab, forked from https://github.com/ot4i/ace-source.

## Installing Copilot in ACE Toolkit
If you are using this code with the IBM ACE Toolkit IDE, it supports the [GitHub Copilot plugin for Eclipse](https://marketplace.eclipse.org/content/github-copilot). 
Just install it from the [Eclipse Marketplace](https://docs.github.com/en/copilot/how-tos/set-up/install-copilot-extension?tool=eclipse) and then sign-in with a GitHub account.

## Custom Instructions

I have added custom instructions files in the [.github](.github/) folder:
- One main [copilot-instructions.md](.github/copilot-instructions.md) file, which is used on any Copilot prompt
- Example scoped instructions files for [ESQL](.github/instructions/esql.instructions.md) and [MSGFLOW](.github/instructions/msgflow.instructions.md) development, linked from copilot-instructions

You can copy these files to your own repository, and extend them with more of your own development guidelines and coding standards.

To learn more about custom instructions in GitHub Copilot, see [Adding repository custom instructions for GitHub Copilot]( https://docs.github.com/en/copilot/how-tos/configure-custom-instructions/add-repository-instructions).

To see examples of custom instructions for other languages, see the [Awesome Copilot](https://github.com/github/awesome-copilot) community repository.