# Demo commands

#### Genval executable from main branch  commit-hash: c52ebb0


## Dockerfile generation and validation

All the policies and input can also be provided from Remote URLs like Github as well.
A auth needs to be set up for genval to communicate with remote for pulling the input and policies.
This is how you can do that:

`export GITHUB_TOEN=<Your GITHUB TOEN>`

```shell
./genval dockerfile --reqinput ./input-templates/dockerfile_input/clang_input.json \
--output ./output/Dockerfile-save \
--inputpolicy ./defaultpolicies/rego/input_policies/ \
--outputpolicy ./defaultpolicies/rego/dockerfile_policies
```


## Validating available Dockerfile

```shell
./genval regoval dockerfileval --reqinput ./Dockerfile \
--policy ./defaultpolicies/rego/dockerfile_policies
```

## Validating Kubernetes manifests

```shell
 ./genval regoval infrafile --reqinput ./input-templates/k8s/deployment.json \
 --policy ./defaultpolicies/rego/infrafile_policies
 ```

## Validating Terraform files with Rego Policies

```shell
./genval regoval terraform --reqinput ./input-templates/terraform/sec_group.tf \
--policy ./defaultpolicies/rego/terraform_policies/terraform
```

## Validating with CEL policies

```shell
./genval celval infrafile --reqinput ./input-templates/k8s/deployment.json \
--policy ./defaultpolicies/cel/k8s_cel.yaml
```

## Push policies, generated/validated config files to OCI registries

### A Note on Genval's Authentication mechanism with Container registries 

To facilitate authentication with container registries, Genval follows this process:

- Environment Variables for Username and Password:

    - Genval checks for the environment variables `ARTIFACT_REGISTRY_USERNAME` and `ARTIFACT_REGISTRY_PASSWORD` for authentication.

- Environment Variable for **Token**:

    - If the **Username** and **Password** environment variables are not found, Genval will then look for the environment variable `ARTIFACT_REGISTRY_TOKEN`.

- Docker Configuration File:

    - If none of the above environment variables are set, Genval will check for the default Docker configuration file located at `$HOME/.docker/config.json` for authentication credentials.


      Some times with `.docker/config.json` users may encounter some errors while interacting with registries. Please login and logout from registry account to resolve the issue:

  - **Login to Container Registry**:
  
    `echo <GITHUB PAT> | echo docker login ghcr.io -u <username> --password-stdin>`

    once login try performing push/pull operations. if succeeds. Great!!!

    If not, Try following:

  - **Logout from Container Registry**:
 
    `docker logout ghcr.io`

    This step should authenticate and allow you to perform push/pull actions on the registry, provided your credentials were passed in correctly.

    Please ensure that at least one of these authentication mechanisms is set up when interacting with container registries.

```shell
./genval artifact push --reqinput ./defaultpolicies/rego/dockerfile_policies/ \
--dest ghcr.io/santoshkal/policyhub/dockerfile-policies:v0.0.1 \
--sign true \
--annotations "authors=ksantoshkal@intelops.dev
```
### Output:
```shell
➜  ./genval-demo git:(main) ✗ ./genval artifact push --reqinput ./defaultpolicies/rego/dockerfile_policies/ \
--dest ghcr.io/santoshkal/policyhub/dockerfile-policies:v0.0.1 \
--sign true \
--annotations "author=santoshkal@intelops.dev"
INFO[0000] Building artifact from: ./defaultpolicies/rego/dockerfile_policies/ 
INFO[0000] ✔ Artifact created successfully              
INFO[0003] cosign: Enter the verification code XNFD-RDQF in your browser at: https://oauth2.sigstore.dev/auth/device?user_code=XNFD-RDQF 
INFO[0003] cosign: Code will be valid for 300 seconds   
INFO[0019] cosign: Token received!                      
INFO[0027] cosign: Generating ephemeral keys...         
INFO[0027] cosign: Retrieving signed certificate...     
INFO[0027] cosign: Non-interactive mode detected, using device flow. 
INFO[0027] cosign: Successfully verified SCT...         
INFO[0027] cosign:                                      
INFO[0027] cosign:      The sigstore service, hosted by sigstore a Series of LF Projects, LLC, is provided pursuant to the Hosted Project Tools Terms of Use, available at https://lfprojects.org/policies/hosted-project-tools-terms-of-use/. 
INFO[0027] cosign:      Note that if your submission includes personal data associated with this signed artifact, it will be part of an immutable record. 
INFO[0027] cosign:      This may include the email address associated with the account with which you authenticate your contractual Agreement. 
INFO[0027] cosign:      This information will be used for signing this artifact and will be stored in public transparency logs and cannot be removed later, and is subject to the Immutable Record notice at https://lfprojects.org/policies/hosted-project-tools-immutable-records/. 
INFO[0027] cosign:                                      
INFO[0027] cosign: By typing 'y', you attest that (1) you are not submitting the personal data of any other person; and (2) you understand and agree to the statement and the Agreement terms at the URLs listed above. 
INFO[0027] cosign: tlog entry created with index: 102571795 
INFO[0027] cosign: Pushing signature to: ghcr.io/santoshkal/policyhub/dockerfile-policies 
INFO[0027] ✔ Artifact pushed successfully to: ghcr.io/santoshkal/policyhub/dockerfile-policies:v0.0.1
, with Digest: sha256:d367ecba061823b2cb59ee853b76be797ce724c3157acc8046d03d5f3ae496b4 
INFO[0027] Digest URL: ghcr.io/santoshkal/policyhub/dockerfile-policies@sha256:d367ecba061823b2cb59ee853b76be797ce724c3157acc8046d03d5f3ae496b4 
```

## Verify and pull an artifact from OCI registry

```shell
./genval artifact pull --dest ghcr.io/santoshkal/policyhub/dockerfile-policies:v0.0.1 \
--path ./output \
--verify true
```
### Output
> The contents of the artifact will be extracted in the directory provided in the `path` flag, `./output` in above command. 
```shell
➜  ./genval-demo git:(main) ✗ ./genval artifact pull --dest ghcr.io/santoshkal/policyhub/dockerfile-policies:v0.0.1 \
> --path ./output \
> --verify true
⣯ Verifying artifact
Verification for ghcr.io/santoshkal/policyhub/dockerfile-policies:v0.0.1 --
The following checks were performed on each of these signatures:
  - The cosign claims were validated
  - Existence of the claims in the transparency log was verified offline
  - The code-signing certificate was verified using trusted certificate authority certificates
Certificate subject: ksantosh@intelops.dev
Certificate issuer URL: https://github.com/login/oauth
Artifact from ghcr.io/santoshkal/policyhub/dockerfile-policies:v0.0.1 pulled and stored in :./output
```


# CUELANG
Create a workspace using `cuemod` command for working with cue:
- create a `cue.mod` and necessary structure for working with `cue` command
```shell
./genval ccuemod init --tool=k8s:1.29
```

Now place all the policies `.cue` policis files under `./K8S_1.29/extracted_content/policy` directory and provide the path to `--policy` flag while using `cue` command as following:

Generate application kubernetes configuration from bare minimum config files :
> **Due to some breaking changes in `cue:v0.9.0` on 12th June some functionality has changed.**

```shell
./genval cue --source ./cuelang/k8s/ \               
--resource Application \
--policy ./cuelang/policy/
```

## Output
```shell
Application validation succeeded, generated manifests in 'output' directory:
 - output/deploy.yaml
 - output/service.yaml
 ```
