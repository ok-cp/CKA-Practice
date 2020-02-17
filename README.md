# CKA-Practice
Prepare for the Certified Kubernetes Administrators Certification

##Kubectl Autocomplete
<p>

```bash
BASH
source <(kubectl completion bash) # setup autocomplete in bash into the current shell, bash-completion package should be installed first.
echo "source <(kubectl completion bash)" >> ~/.bashrc # add autocomplete permanently to your bash shell.
```

</p>
You can also use a shorthand alias for kubectl that also works with completion:
<p>

```bash
alias k=kubectl
complete -F __start_kubectl k
```

</p>

## Contents


- [Application Lifecycle Management 8%](ap-life-mg.md)
- [Installation, Configuration & Validation 12%](install-conf-valid.md)
- [Core Concepts 19%](core.md)
- [Networking 11%](network.md)
- [Scheduling 5%](schedule.md)
- [Security 12%](security.md)
- [Cluster Maintenance 11%](cluster-mc.md)
- [Logging / Monitoring 5%](logging-monitor.md)
- [Storage 7%](storage.md)
- [Troubleshooting 10%](trouble.md)
