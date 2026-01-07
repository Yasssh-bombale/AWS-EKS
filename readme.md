# AWS EKS Learning Roadmap & Setup Guide

Comprehensive guide for setting up Amazon EKS on macOS (M1 Mac compatible). Covers installation, cluster creation, basic operations, and **critical cleanup to avoid bills** .[aws.amazon**+1**](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)

## Prerequisites

- macOS with Terminal/iTerm
- AWS account (Free Tier eligible)
- AWS CLI v2 configured (`aws configure`)
- IAM user with AdministratorAccess (or EKS/EC2/IAM/VPC permissions)

  **Cost Warning** : EKS costs ~$0.10/hour + EC2 nodes + data transfer. Always cleanup![aws.amazon](https://docs.aws.amazon.com/eks/latest/eksctl/installation.html)

## 1. Install Tools

<pre class="not-prose w-full rounded font-mono text-sm font-extralight"><div class="codeWrapper text-light selection:text-super selection:bg-super/10 my-md relative flex flex-col rounded-lg font-mono text-sm font-normal bg-subtler"><div class="translate-y-xs -translate-x-xs bottom-xl mb-xl flex h-0 items-start justify-end sm:sticky sm:top-xs"><div class="overflow-hidden rounded-full border-subtlest ring-subtlest divide-subtlest bg-base"><div class="border-subtlest ring-subtlest divide-subtlest bg-subtler"><button data-testid="copy-code-button" aria-label="Copy code" type="button" class="focus-visible:bg-subtle hover:bg-subtle text-quiet  hover:text-foreground dark:hover:bg-subtle font-sans focus:outline-none outline-none outline-transparent transition duration-300 ease-out select-none items-center relative group/button font-semimedium justify-center text-center items-center rounded-full cursor-pointer active:scale-[0.97] active:duration-150 active:ease-outExpo origin-center whitespace-nowrap inline-flex text-sm h-8 aspect-square" data-state="closed"><div class="flex items-center min-w-0 gap-two justify-center"><div class="flex shrink-0 items-center justify-center size-4"><svg role="img" class="inline-flex fill-current" width="16" height="16"><use xlink:href="#pplx-icon-copy"></use></svg></div></div></button></div></div></div><div class="-mt-xl"><div><div data-testid="code-language-indicator" class="text-quiet bg-subtle py-xs px-sm inline-block rounded-br rounded-tl-lg text-xs font-thin">bash</div></div><div><span><code><span><span class="token token"># AWS CLI (if not done)</span><span>
</span></span><span><span>brew </span><span class="token token">install</span><span> awscli
</span></span><span>aws configure
</span><span>
</span><span><span></span><span class="token token"># kubectl</span><span>
</span></span><span><span>brew </span><span class="token token">install</span><span> kubectl
</span></span><span>kubectl version --client
</span><span>
</span><span><span></span><span class="token token"># eksctl</span><span>
</span></span><span>brew tap aws/tap
</span><span><span>brew </span><span class="token token">install</span><span> aws/tap/eksctl
</span></span><span>eksctl version
</span><span></span></code></span></div></div></div></pre>

Verify: `aws sts get-caller-identity`[kubernetes**+2**](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/)

## 2. Create EKS Cluster

**Basic 2-node cluster** (t3.medium = ~$0.04/hr each):

<pre class="not-prose w-full rounded font-mono text-sm font-extralight"><div class="codeWrapper text-light selection:text-super selection:bg-super/10 my-md relative flex flex-col rounded-lg font-mono text-sm font-normal bg-subtler"><div class="translate-y-xs -translate-x-xs bottom-xl mb-xl flex h-0 items-start justify-end sm:sticky sm:top-xs"><div class="overflow-hidden rounded-full border-subtlest ring-subtlest divide-subtlest bg-base"><div class="border-subtlest ring-subtlest divide-subtlest bg-subtler"><button data-testid="copy-code-button" aria-label="Copy code" type="button" class="focus-visible:bg-subtle hover:bg-subtle text-quiet  hover:text-foreground dark:hover:bg-subtle font-sans focus:outline-none outline-none outline-transparent transition duration-300 ease-out select-none items-center relative group/button font-semimedium justify-center text-center items-center rounded-full cursor-pointer active:scale-[0.97] active:duration-150 active:ease-outExpo origin-center whitespace-nowrap inline-flex text-sm h-8 aspect-square" data-state="closed"><div class="flex items-center min-w-0 gap-two justify-center"><div class="flex shrink-0 items-center justify-center size-4"><svg role="img" class="inline-flex fill-current" width="16" height="16"><use xlink:href="#pplx-icon-copy"></use></svg></div></div></button></div></div></div><div class="-mt-xl"><div><div data-testid="code-language-indicator" class="text-quiet bg-subtle py-xs px-sm inline-block rounded-br rounded-tl-lg text-xs font-thin">bash</div></div><div><span><code><span><span>eksctl create cluster </span><span class="token token punctuation">\</span><span>
</span></span><span><span>  --name my-cluster </span><span class="token token punctuation">\</span><span>
</span></span><span><span>  --region ap-south-1 </span><span class="token token punctuation">\</span><span>
</span></span><span><span>  --nodegroup-name linux-nodes </span><span class="token token punctuation">\</span><span>
</span></span><span><span>  --node-type t3.medium </span><span class="token token punctuation">\</span><span>
</span></span><span><span>  --nodes </span><span class="token token">2</span><span></span><span class="token token punctuation">\</span><span>
</span></span><span><span>  --nodes-min </span><span class="token token">1</span><span></span><span class="token token punctuation">\</span><span>
</span></span><span><span>  --nodes-max </span><span class="token token">3</span><span></span><span class="token token punctuation">\</span><span>
</span></span><span>  --managed
</span><span></span></code></span></div></div></div></pre>

example 
```
eksctl create cluster --name first-cluster --region ap-south-1 --node-type t3.micro --nodes-min 2 --nodes-max 3
```

**Timeline** : 15-20 mins. Monitors: `eksctl get clusters`[aws.amazon](https://docs.aws.amazon.com/eks/latest/eksctl/installation.html)

## 3. Verify Cluster

<pre class="not-prose w-full rounded font-mono text-sm font-extralight"><div class="codeWrapper text-light selection:text-super selection:bg-super/10 my-md relative flex flex-col rounded-lg font-mono text-sm font-normal bg-subtler"><div class="translate-y-xs -translate-x-xs bottom-xl mb-xl flex h-0 items-start justify-end sm:sticky sm:top-xs"><div class="overflow-hidden rounded-full border-subtlest ring-subtlest divide-subtlest bg-base"><div class="border-subtlest ring-subtlest divide-subtlest bg-subtler"><button data-testid="copy-code-button" aria-label="Copy code" type="button" class="focus-visible:bg-subtle hover:bg-subtle text-quiet  hover:text-foreground dark:hover:bg-subtle font-sans focus:outline-none outline-none outline-transparent transition duration-300 ease-out select-none items-center relative group/button font-semimedium justify-center text-center items-center rounded-full cursor-pointer active:scale-[0.97] active:duration-150 active:ease-outExpo origin-center whitespace-nowrap inline-flex text-sm h-8 aspect-square" data-state="closed"><div class="flex items-center min-w-0 gap-two justify-center"><div class="flex shrink-0 items-center justify-center size-4"><svg role="img" class="inline-flex fill-current" width="16" height="16"><use xlink:href="#pplx-icon-copy"></use></svg></div></div></button></div></div></div><div class="-mt-xl"><div><div data-testid="code-language-indicator" class="text-quiet bg-subtle py-xs px-sm inline-block rounded-br rounded-tl-lg text-xs font-thin">bash</div></div><div><span><code><span><span class="token token"># Update kubeconfig (auto-done by eksctl)</span><span>
</span></span><span>aws eks update-kubeconfig --name my-cluster --region ap-south-1
</span><span>
</span><span><span></span><span class="token token"># Test access</span><span>
</span></span><span>kubectl get nodes
</span><span>kubectl get svc
</span><span>kubectl get pods --all-namespaces
</span><span></span></code></span></div></div></div></pre>

**eksctl uses AWS CLI credentials. kubectl uses kubeconfig → AWS get-token.** [aws.amazon](https://docs.aws.amazon.com/eks/latest/userguide/create-kubeconfig.html)

## 4. Deploy Sample App

<pre class="not-prose w-full rounded font-mono text-sm font-extralight"><div class="codeWrapper text-light selection:text-super selection:bg-super/10 my-md relative flex flex-col rounded-lg font-mono text-sm font-normal bg-subtler"><div class="translate-y-xs -translate-x-xs bottom-xl mb-xl flex h-0 items-start justify-end sm:sticky sm:top-xs"><div class="overflow-hidden rounded-full border-subtlest ring-subtlest divide-subtlest bg-base"><div class="border-subtlest ring-subtlest divide-subtlest bg-subtler"><button data-testid="copy-code-button" aria-label="Copy code" type="button" class="focus-visible:bg-subtle hover:bg-subtle text-quiet  hover:text-foreground dark:hover:bg-subtle font-sans focus:outline-none outline-none outline-transparent transition duration-300 ease-out select-none items-center relative group/button font-semimedium justify-center text-center items-center rounded-full cursor-pointer active:scale-[0.97] active:duration-150 active:ease-outExpo origin-center whitespace-nowrap inline-flex text-sm h-8 aspect-square" data-state="closed"><div class="flex items-center min-w-0 gap-two justify-center"><div class="flex shrink-0 items-center justify-center size-4"><svg role="img" class="inline-flex fill-current" width="16" height="16"><use xlink:href="#pplx-icon-copy"></use></svg></div></div></button></div></div></div><div class="-mt-xl"><div><div data-testid="code-language-indicator" class="text-quiet bg-subtle py-xs px-sm inline-block rounded-br rounded-tl-lg text-xs font-thin">bash</div></div><div><span><code><span><span class="token token"># Create deployment</span><span>
</span></span><span><span>kubectl create deployment nginx --image</span><span class="token token operator">=</span><span>nginx
</span></span><span>
</span><span><span></span><span class="token token"># Expose as LoadBalancer (creates AWS NLB!)</span><span>
</span></span><span><span>kubectl expose deployment nginx --port</span><span class="token token operator">=</span><span class="token token">80</span><span> --type</span><span class="token token operator">=</span><span>LoadBalancer
</span></span><span>
</span><span><span></span><span class="token token"># Check</span><span>
</span></span><span>kubectl get svc
</span><span>kubectl get nodes -o wide
</span><span></span></code></span></div></div></div></pre>

**Get Load Balancer URL** : `kubectl get svc nginx` → EXTERNAL-IP[aws.amazon](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)

## 5. Scale & Monitor

<pre class="not-prose w-full rounded font-mono text-sm font-extralight"><div class="codeWrapper text-light selection:text-super selection:bg-super/10 my-md relative flex flex-col rounded-lg font-mono text-sm font-normal bg-subtler"><div class="translate-y-xs -translate-x-xs bottom-xl mb-xl flex h-0 items-start justify-end sm:sticky sm:top-xs"><div class="overflow-hidden rounded-full border-subtlest ring-subtlest divide-subtlest bg-base"><div class="border-subtlest ring-subtlest divide-subtlest bg-subtler"><button data-testid="copy-code-button" aria-label="Copy code" type="button" class="focus-visible:bg-subtle hover:bg-subtle text-quiet  hover:text-foreground dark:hover:bg-subtle font-sans focus:outline-none outline-none outline-transparent transition duration-300 ease-out select-none items-center relative group/button font-semimedium justify-center text-center items-center rounded-full cursor-pointer active:scale-[0.97] active:duration-150 active:ease-outExpo origin-center whitespace-nowrap inline-flex text-sm h-8 aspect-square" data-state="closed"><div class="flex items-center min-w-0 gap-two justify-center"><div class="flex shrink-0 items-center justify-center size-4"><svg role="img" class="inline-flex fill-current" width="16" height="16"><use xlink:href="#pplx-icon-copy"></use></svg></div></div></button></div></div></div><div class="-mt-xl"><div><div data-testid="code-language-indicator" class="text-quiet bg-subtle py-xs px-sm inline-block rounded-br rounded-tl-lg text-xs font-thin">bash</div></div><div><span><code><span><span class="token token"># Scale deployment</span><span>
</span></span><span><span>kubectl scale deployment nginx --replicas</span><span class="token token operator">=</span><span class="token token">3</span><span>
</span></span><span>
</span><span><span></span><span class="token token"># Auto-scaling (if configured)</span><span>
</span></span><span>kubectl get hpa
</span><span>
</span><span><span></span><span class="token token"># Logs</span><span>
</span></span><span>kubectl logs deployment/nginx
</span><span></span></code></span></div></div></div></pre>

## 6. **CRITICAL: Cleanup (Avoid Bills!)**

## Delete Load Balancer First

Load Balancers don't auto-delete:

<pre class="not-prose w-full rounded font-mono text-sm font-extralight"><div class="codeWrapper text-light selection:text-super selection:bg-super/10 my-md relative flex flex-col rounded-lg font-mono text-sm font-normal bg-subtler"><div class="translate-y-xs -translate-x-xs bottom-xl mb-xl flex h-0 items-start justify-end sm:sticky sm:top-xs"><div class="overflow-hidden rounded-full border-subtlest ring-subtlest divide-subtlest bg-base"><div class="border-subtlest ring-subtlest divide-subtlest bg-subtler"><button data-testid="copy-code-button" aria-label="Copy code" type="button" class="focus-visible:bg-subtle hover:bg-subtle text-quiet  hover:text-foreground dark:hover:bg-subtle font-sans focus:outline-none outline-none outline-transparent transition duration-300 ease-out select-none items-center relative group/button font-semimedium justify-center text-center items-center rounded-full cursor-pointer active:scale-[0.97] active:duration-150 active:ease-outExpo origin-center whitespace-nowrap inline-flex text-sm h-8 aspect-square" data-state="closed"><div class="flex items-center min-w-0 gap-two justify-center"><div class="flex shrink-0 items-center justify-center size-4"><svg role="img" class="inline-flex fill-current" width="16" height="16"><use xlink:href="#pplx-icon-copy"></use></svg></div></div></button></div></div></div><div class="-mt-xl"><div><div data-testid="code-language-indicator" class="text-quiet bg-subtle py-xs px-sm inline-block rounded-br rounded-tl-lg text-xs font-thin">bash</div></div><div><span><code><span><span>kubectl delete svc nginx
</span></span><span><span></span><span class="token token"># Wait 2-3 mins, check AWS Console > EC2 > Load Balancers (delete any remaining)</span><span>
</span></span><span></span></code></span></div></div></div></pre>

## Delete Everything

<pre class="not-prose w-full rounded font-mono text-sm font-extralight"><div class="codeWrapper text-light selection:text-super selection:bg-super/10 my-md relative flex flex-col rounded-lg font-mono text-sm font-normal bg-subtler"><div class="translate-y-xs -translate-x-xs bottom-xl mb-xl flex h-0 items-start justify-end sm:sticky sm:top-xs"><div class="overflow-hidden rounded-full border-subtlest ring-subtlest divide-subtlest bg-base"><div class="border-subtlest ring-subtlest divide-subtlest bg-subtler"><button data-testid="copy-code-button" aria-label="Copy code" type="button" class="focus-visible:bg-subtle hover:bg-subtle text-quiet  hover:text-foreground dark:hover:bg-subtle font-sans focus:outline-none outline-none outline-transparent transition duration-300 ease-out select-none items-center relative group/button font-semimedium justify-center text-center items-center rounded-full cursor-pointer active:scale-[0.97] active:duration-150 active:ease-outExpo origin-center whitespace-nowrap inline-flex text-sm h-8 aspect-square" data-state="closed"><div class="flex items-center min-w-0 gap-two justify-center"><div class="flex shrink-0 items-center justify-center size-4"><svg role="img" class="inline-flex fill-current" width="16" height="16"><use xlink:href="#pplx-icon-copy"></use></svg></div></div></button></div></div></div><div class="-mt-xl"><div><div data-testid="code-language-indicator" class="text-quiet bg-subtle py-xs px-sm inline-block rounded-br rounded-tl-lg text-xs font-thin">bash</div></div><div><span><code><span><span class="token token"># Delete cluster + all resources (nodes, security groups, IAM roles)</span><span>
</span></span><span>eksctl delete cluster --name my-cluster --region ap-south-1
</span><span>
</span><span><span></span><span class="token token"># Verify cleanup</span><span>
</span></span><span>aws eks list-clusters --region ap-south-1
</span><span><span>kubectl config delete-context arn:aws:eks:ap-south-1:</span><span class="token token punctuation">..</span><span>.:cluster/my-cluster
</span></span><span><span>kubectl config delete-cluster arn:aws:eks:ap-south-1:</span><span class="token token punctuation">..</span><span>.:cluster/my-cluster
</span></span><span></span></code></span></div></div></div></pre>

**Timeline** : 5-10 mins. **Costs stop immediately after.** [aws.amazon](https://docs.aws.amazon.com/eks/latest/eksctl/installation.html)

## Cost Summary Table

| Resource            | Hourly Cost (ap-south-1) | Free Tier  |
| ------------------- | ------------------------ | ---------- |
| EKS Control Plane   | $0.10                    | No         |
| t3.medium (2 nodes) | $0.084                   | 750 hrs/mo |
| NLB Load Balancer   | $0.0225 + data           | No         |
| **Total (running)** | **~$0.25/hr**            |            |

**Daily cost if forgotten** : ~$6. **Always delete!** [aws.amazon](https://docs.aws.amazon.com/eks/latest/eksctl/installation.html)

## Next Learning Steps

1. **eksctl utils** : `eksctl utils create-iam-identity-mapping`
2. **Advanced** : Node groups, Fargate, IRSA
3. **CI/CD** : GitHub Actions → EKS
4. **Monitoring** : CloudWatch, Prometheus

## Troubleshooting

<pre class="not-prose w-full rounded font-mono text-sm font-extralight"><div class="codeWrapper text-light selection:text-super selection:bg-super/10 my-md relative flex flex-col rounded-lg font-mono text-sm font-normal bg-subtler"><div class="translate-y-xs -translate-x-xs bottom-xl mb-xl flex h-0 items-start justify-end sm:sticky sm:top-xs"><div class="overflow-hidden rounded-full border-subtlest ring-subtlest divide-subtlest bg-base"><div class="border-subtlest ring-subtlest divide-subtlest bg-subtler"><button data-testid="copy-code-button" aria-label="Copy code" type="button" class="focus-visible:bg-subtle hover:bg-subtle text-quiet  hover:text-foreground dark:hover:bg-subtle font-sans focus:outline-none outline-none outline-transparent transition duration-300 ease-out select-none items-center relative group/button font-semimedium justify-center text-center items-center rounded-full cursor-pointer active:scale-[0.97] active:duration-150 active:ease-outExpo origin-center whitespace-nowrap inline-flex text-sm h-8 aspect-square" data-state="closed"><div class="flex items-center min-w-0 gap-two justify-center"><div class="flex shrink-0 items-center justify-center size-4"><svg role="img" class="inline-flex fill-current" width="16" height="16"><use xlink:href="#pplx-icon-copy"></use></svg></div></div></button></div></div></div><div class="-mt-xl"><div><div data-testid="code-language-indicator" class="text-quiet bg-subtle py-xs px-sm inline-block rounded-br rounded-tl-lg text-xs font-thin">text</div></div><div><span><code><span><span>❌ "Unable to connect to the server": aws eks update-kubeconfig
</span></span><span>❌ "No clusters found": Check AWS Console > EKS
</span><span>❌ "AccessDenied": IAM permissions
</span><span>❌ High bills: Check EC2/ELB in AWS Console → TERMINATE!
</span><span></span></code></span></div></div></div></pre>

**Bookmark this README. Run cleanup daily during learning.** [aws.amazon**+2**](https://docs.aws.amazon.com/eks/latest/userguide/create-kubeconfig.html)

1. [https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)
2. [https://docs.aws.amazon.com/eks/latest/eksctl/installation.html](https://docs.aws.amazon.com/eks/latest/eksctl/installation.html)
3. [https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/)
4. [https://formulae.brew.sh/formula/kubernetes-cli](https://formulae.brew.sh/formula/kubernetes-cli)
5. [https://docs.aws.amazon.com/eks/latest/userguide/create-kubeconfig.html](https://docs.aws.amazon.com/eks/latest/userguide/create-kubeconfig.html)
