# EC2 File Transfer — Two Instances (public IPs)

This document shows common, secure ways to transfer files between two EC2 instances that each have public IPs (Instance A and Instance B). Replace placeholders like `<KEY.pem>`, `<A_IP>`, `<B_IP>`, and paths with real values.

## Assumptions
- Both instances are accessible over SSH (port 22) and you have the `.pem` key for the `ubuntu` (or appropriate) user.
- Security groups allow SSH from your IP and, if transferring directly between instances, allow SSH from the other instance's IP.
- You have `rsync`, `scp`, `tar`, and `aws` CLI available where applicable.

## Transfer scenarios

- From your local machine to Instance A or B: use `scp` or `rsync` with `-i`.
- Between Instance A and Instance B directly: scp/rsync from one instance to the other (requires key on source or agent forwarding).
- If keys are only on your local machine: use SSH agent forwarding (`ssh -A`) or relay via your local host.
- For many files/large uploads: use `rsync` or stream with `tar` over SSH.

## Examples

1) scp: local → Instance A

```bash
scp -i ~/keys/my-key.pem /local/path/file.txt ubuntu@<A_IP>:/home/ubuntu/
```

2) scp (direct) Instance A → Instance B (key present on A)

On Instance A (has `/home/ubuntu/.ssh/id_rsa` or `my-key.pem`):

```bash
# from A
scp -i /path/to/my-key.pem -r /path/to/dir ubuntu@<B_IP>:/home/ubuntu/dest
```

3) scp via your local machine (relay) — key only locally

```bash
# copy from local to A, then from A to B using agent forwarding
ssh -A -i ~/keys/my-key.pem ubuntu@<A_IP>
# now on A, run (will use forwarded key):
scp -i ~/keys/my-key.pem -r /path/on/A ubuntu@<B_IP>:/home/ubuntu/dest
```

4) rsync (recommended for directories and resume support)

Local → Instance B:

```bash
rsync -avz --progress -e "ssh -i ~/keys/my-key.pem" /local/dir/ ubuntu@<B_IP>:/home/ubuntu/dest/
```

Instance A → Instance B (from local machine using ProxyJump):

```bash
# if you want to run rsync from your local host relaying through A to reach B
rsync -avz -e "ssh -J ubuntu@<A_IP> -i ~/keys/my-key.pem" /local/dir/ ubuntu@<B_IP>:/home/ubuntu/dest/
```

5) Stream with tar over SSH (fast for many small files)

```bash
# from local (send to B)
tar -C /local/path -czf - . | ssh -i ~/keys/my-key.pem ubuntu@<B_IP> 'tar -C /home/ubuntu/dest -xzf -'

# between A and B (run on A, key available)
tar -C /path/to/dir -czf - . | ssh -i /path/to/key.pem ubuntu@<B_IP> 'tar -C /home/ubuntu/dest -xzf -'
```

6) Using AWS S3 as an intermediary (good for very large transfers or when direct SSH is restricted)

```bash
# from local or A: upload to S3
aws s3 cp /local/path/largefile.tar.gz s3://my-bucket/path/
# on B: download
aws s3 cp s3://my-bucket/path/largefile.tar.gz /home/ubuntu/
```

7) Using `nc` (netcat) for LAN/private transfers — only for trusted networks

On B (listening):

```bash
sudo apt install -y netcat
nc -l -p 9000 > /home/ubuntu/file.tar.gz
```

On A (send):

```bash
cat /path/to/file.tar.gz | nc <B_IP> 9000
```

Note: `nc` is unencrypted and should only be used in isolated/trusted networks.

## Useful tips
- Use `ssh-agent` to avoid copying keys to instances: `eval "$(ssh-agent -s)"; ssh-add ~/keys/my-key.pem` then `ssh -A` to forward.
- Keep file ownership/permissions correct after transfer: `sudo chown -R ubuntu:ubuntu /path/dest`.
- For large/unstable transfers use `rsync --partial --progress --bwlimit=...` to resume and limit bandwidth.
- Verify transfer integrity with `sha256sum` on sender and receiver.
- If you get `Permission denied (publickey)`, ensure the correct key and `chmod 400` on the `.pem` file.

## Troubleshooting
- Host key mismatch: remove old host key with `ssh-keygen -R <IP>` then reconnect.
- Firewall/Security Group blocking: ensure source/destination IPs are allowed on port 22.
- If keys are only local and you cannot agent-forward, copy the public key to the other instance: `ssh-copy-id -i ~/.ssh/id_rsa.pub ubuntu@<B_IP>`.

---
Want me to also:
- Add a small `transfer.sh` helper script to the repo? (examples: scp/rsync wrappers)
- Add an `rsync` systemd service or `screen`/`tmux` instructions for long-running transfers?

Tell me which and I'll add it.