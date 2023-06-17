title: Configuring github pages
created: 6 June 2023
tags:
  - introduction
short: Using static site generators and github pages
---
# Generating github.io site

## Static site generators
A modern way of creating static site is to use static site generator. By searching on google for static site generators you can examine multiple options for your site. Combining this approach with idea of hosting (for free) static site on github.io it made sence to try with recomended (default) generator for github (jekyll).

Tried to use jekyll with chirpy theme and after reading some texts and instalation/configuration guides decides to abbandon this approach. Main issue , along installation of ruby on windows, was incompatible nogokumbo package with jekyll. Error stated that html-proofer with version 3.18 could not be executed successfully and some articles suggested using version 3.16 but then error incompatible nogokumbo lib was detected.

Abandoning jekyll (which looks really nice) and reverting to home ground , dotnet :-)

Even though that wyam is not officially supported anymore , in favor of Soniq, i decided to use it. Really liked dotnet tool approach and powerfull pipelines that could be extended. On the down side , themes are not really developed part of the ecosystem.

## Hosting
github.io offers ,as mentined , free hosting of static sites. This feature can be used with github actions to integrate deployment and developing, awesome.

## Development
Following simple instructions from wyam ended up with generated site in 10 minutes.

Writing some text as first blog took a bit more :-)

## Deployment
As main guide used this blog https://ewinnington.github.io/posts/wyam-github-actions but found some differences.
I didn;t like the idea of using source and master branches , main is defualt anyway so switched this to main and gh-pages branches.
ssh-keygen command states that option -N should be used when generating ssh key but in my case this option should be removed.

Some screens looked differently in current version of github ui then ones shown on screen , but this is understandable.

Initially deploy action failed to deploy caused by an error:
```
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

Reading some articles about deployment action and found https://docs.github.com/en/authentication/troubleshooting-ssh/error-permission-denied-publickey
so looks like user should be git@github.com

regenerating ssh keys, reconfiguring settings on repository.

## Final
i investigated potential options for using static site generatos hosted by github.io 

Decided to use wyam and configured CI/CD pipeline for deployment.
Wrote this peace of text as a first article.

Happy