---
layout: post
title:
  Working with Multiple Subscriptions, AKS Clusters, and K8s Namespaces in Azure
  CLI
description:
  Working with Multiple Subscriptions, AKS Clusters, and K8s Namespaces
  in Azure CLI
image: /assets/img/posts/2021-10-06-working-with-multiple-subscriptions-in-azure-cli/k8s2.jpg
category: [Azure, AKS]
tags: [cloud, k8s, kubernetes, azurecli, cli, aks]
date: 2021-10-06 16:00 -0400
---

## Working with Multiple Subscriptions, AKS Clusters, and K8s Namespaces in Azure CLI

Have you had to work with multiple Azure subscriptions, AKS clusters, and namespaces? It is time-consuming to jump on a different cluster while working on one, especially on another subscription. You have to set the subscription, get the aks credentials, and set the namespace — if you don’t want to add `--namespace=…` to all your commands. Then, you can run your kubectl command. There must be an easier way. Well, I had that issue, and — as every lazy engineer would do — I wrote a script to make it easier.

## How it works

When you log in to Azure from azure CLI, it stores the tokens and your account information in a particular folder named `.azure` by default under your home directory. It also keeps your subscription information in the same directory when you set your subscription (`az account set -s <subscription name>`). When you get aks credentials (`az aks get-credentials -n <cluster name> -g <resource group>`), Azure CLI stores your cluster information (a.k.a. context info) in the kubectl config folder. It is the `.kube` folder under your home directory. The kubectl uses the context information in that folder to make API calls to retrieve the data or perform the actions you want.

The fun part starts when you switch to another subscription. Your account information gets updated in the `.azure` folder, but all the information in the `.kube` folder stays the same. Since all the information belongs to the other account, you need to get aks credentials again so that Azure CLI will update context information in the `.kube` folder for the new subscription. If you don’t use the default namespace, which is a common practice not to use, you need to set your namespace, too. Again, it is necessary only if you don’t want to add `--namespace=…` to all your kubectl commands. Azure CLI will also prompt you to log in one more time to refresh the tokens for the new subscription. Long story short, you will need to do

- `az account set -s <subscription>`
- `az aks get-credentials -n <cluster name> -g <resource group>`
- `kubectl config set-context --current --namespace=<namespace>`
- login into Azure with a device code

each time you switch to another subscription.

## Solution

Both Azure CLI and kubectl let you set your config directories by setting the `AZ_CONFIG_DIR` and `KUBECONFIG` environment variables, respectively. If those variables are not set, Azure CLI and kubectl use the default directories mentioned above for the config folder.

I have four subscriptions that I am working on every day. I created a folder for each of my subscription/environments in my home directory, and I wrote a script to set those variables dynamically.

![Folder](/assets/img/posts/2021-10-06-working-with-multiple-subscriptions-in-azure-cli/k8s1.jpg)

### For macOS (zsh shell or bash):

```shell
export AZURE_CONFIG_DIR=~/az-profiles/dev
export KUBECONFIG=~/az-profiles/dev/kubeconfig.yaml

function azprofile {
    readonly DEVPATH=~/az-profiles/dev
    readonly QAPATH=~/az-profiles/qa
    readonly UATPATH=~/az-profiles/uat
    readonly PERFPATH=~/az-profiles/perf

    readonly ERRORMSG="Invalid usage. Usage: azprofile [show|set] [dev|qa|uat|perf|default]"

    readonly CMD=${1:?"$ERRORMSG"}

    if [ "$CMD" = "show" ]; then
        if [ -z "$AZURE_CONFIG_DIR" ]; then
            echo "The current az-cli config dir: ~/.azure"
        else
            echo "The current az-cli config dir: $AZURE_CONFIG_DIR"
        fi
        if [ -z "$KUBECONFIG" ]; then
            echo "The current kubectl config dir: ~/.kube"
        else
            echo "The current kubectl config dir: $KUBECONFIG"
        fi
    elif [ $CMD = "set" ]; then
        readonly AZENV=${2:?"$ERRORMSG"}
        if [ $AZENV = "dev" ]; then
            export AZURE_CONFIG_DIR=$DEVPATH
            export KUBECONFIG=$DEVPATH/kubeconfig.yaml
            echo "Azure config directory has been set to $DEVPATH"
        elif [ $AZENV = "qa" ]; then
            export AZURE_CONFIG_DIR=$QAPATH
            export KUBECONFIG=$QAPATH/kubeconfig.yaml
            echo "Azure config directory has been set to $QAPATH"
        elif [ $AZENV = "uat" ]; then
            export AZURE_CONFIG_DIR=$UATPATH
            export KUBECONFIG=$UATPATH/kubeconfig.yaml
            echo "Azure config directory has been set to $UATPATH"
        elif [ $AZENV = "perf" ]; then
            export AZURE_CONFIG_DIR=$PERFPATH
            export KUBECONFIG=$PERFPATH/kubeconfig.yaml
            echo "Azure config directory has been set to $PERFPATH"
        elif [ $AZENV = "default" ]; then
            unset AZURE_CONFIG_DIR
            unset KUBECONFIG
            echo "Azure config directory has been set to default directory"
        else
            echo "$ERRORMSG"
        fi
    else
        echo "$ERRORMSG"
    fi
}
```

### For Windows (PowerShell):

```powershell
function Get-AZProfile {
    [CmdletBinding()]
    param (

    )

    begin {
        $OutputHolder = @{
            AzureConfigPath = "$Home\.azure";
            KubeConfigPath  = "$Home\.kube";
        }
    }

    process {
        if($Env:AZURE_CONFIG_DIR) {
            $OutputHolder.AzureConfigPath = $Env:AZURE_CONFIG_DIR
        }

        if($Env:KUBECONFIG) {
            $OutputHolder.KubeConfigPath = $Env:KUBECONFIG
        }
    }

    end {
        Write-Output ([PSCustomObject] $OutputHolder)
    }
}

function Set-AZProfile {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory, HelpMessage="Azure Profile Environment")]
        [ValidateSet("dev","qa","uat","perf","default")]
        [string] $AZEnvironment
    )

    begin {
        $DevPath  = "$Home/Az-Profiles/dev"
        $QAPath   = "$Home/Az-Profiles/qa"
        $UATPath  = "$Home/Az-Profiles/uat"
        $PERFPath = "$Home/Az-Profiles/perf"
    }

    process {
        switch ($AZEnvironment) {
            "dev" {
                $Env:AZURE_CONFIG_DIR = $DevPath
                $Env:KUBECONFIG       = "$DevPath/kubeconfig.yml"
             }
            "qa" {
                $Env:AZURE_CONFIG_DIR = $QAPath
                $Env:KUBECONFIG       = "$QAPath/kubeconfig.yml"
             }
            "uat" {
                $Env:AZURE_CONFIG_DIR = $UATPath
                $Env:KUBECONFIG       = "$UATPath/kubeconfig.yml"
             }
            "perf" {
                $Env:AZURE_CONFIG_DIR = $PERFPath
                $Env:KUBECONFIG       = "$PERFPath/kubeconfig.yml"
             }
            "default" {
                $Env:AZURE_CONFIG_DIR = $null
                $Env:KUBECONFIG       = $null
            }
        }
    }

    end {
        Write-Host "Azure Config profile has been set to: $AZEnvironment"
    }
}
```

## How Scripts Work

In macOS, you will need to add the first script to the end of your `~/.zshrc` file if you use zsh or `~/.bashrc` if you use bash.

For Windows, you will need to add the PowerShell script, the second one, to the end of the `Profile.ps1` file under `<your documents folder path>\WindowsPowerShell\` folder if you use Windows PowerShell ( version ≤ 5.1) or `<your documents folder path>\PowerShell\` if you use PowerShell (version ≥ 6). If you don’t have the `[Windows]PowerShell` folder or `Profile.ps1` file, you can create it under your Documents folder.

The script accepts two commands; **show** and **set**.

`azprofile show` (`Get-AZProfile` for PowerShell) shows the current configuration.

`azprofile set <profile name>` (`Set-AZProfile <profile name>` for PowerShell) – as you can guess – sets the subscription or environment

> Even though both scripts do the same thing, they are slightly different because of the concepts of \*nix shells and PowerShell.

![azget](/assets/img/posts/2021-10-06-working-with-multiple-subscriptions-in-azure-cli/k8s2.jpg)

![azset](/assets/img/posts/2021-10-06-working-with-multiple-subscriptions-in-azure-cli/k8s3.jpg)

> **Note1:** I have an alias for the kubectl command. The `k` commands in the screenshots refer to `kubectl` (`alias k="kubectl"`)

> **Note2:** You will need to login to azure with a device code when you set your environment for the first time.
