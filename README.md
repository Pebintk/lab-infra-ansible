# lab-ansible

OS-level configuration for the Jenkins controller VM. Terraform (`lab-infra/jenkins/`) owns
the VM itself; this repo owns everything inside it — Docker, Jenkins, and the credential
helper for pushing to Artifact Registry.

## Prerequisites

- `gcloud auth application-default login` — the dynamic inventory reuses these credentials
  (no service account key files are used anywhere in this repo)
- Collections installed: `ansible-galaxy collection install -r requirements.yml`
- The Jenkins VM running in GCP with the `role = jenkins` label set

## Run it

```bash
ansible-playbook site.yml
ansible-playbook site.yml   # run again — should report changed=0
```

The second run reporting `changed=0` is the acceptance test for this repo.

## Access Jenkins

Retrieve the initial unlock password:

```bash
ansible role_jenkins -b -m command -a "cat /var/lib/jenkins/secrets/initialAdminPassword"
```

Port 8080 is deliberately not open in the firewall — tunnel to it over SSH:

```bash
ssh -L 8080:localhost:8080 bintang@<vm-ip>
```

Then open http://localhost:8080 in a browser.
