# Simple SCP Text Transfer

Use `scp` when you want to copy a text file between two EC2 instances that have public IPs.

## Copy from Instance A to Instance B

```bash
scp -i ~/keys/my-key.pem /home/ubuntu/message.txt ubuntu@<B_PUBLIC_IP>:/home/ubuntu/
```

## Copy from Instance B to Instance A

```bash
scp -i ~/keys/my-key.pem /home/ubuntu/message.txt ubuntu@<A_PUBLIC_IP>:/home/ubuntu/
```

## Copy from your local machine to an EC2 instance

```bash
scp -i ~/keys/my-key.pem message.txt ubuntu@<EC2_PUBLIC_IP>:/home/ubuntu/
```

## Copy a text file back to your local machine

```bash
scp -i ~/keys/my-key.pem ubuntu@<EC2_PUBLIC_IP>:/home/ubuntu/message.txt .
```

## Notes
- Replace `ubuntu` with the correct SSH user if your AMI uses a different one.
- Make sure the security group allows SSH on port `22`.
- If you get `Permission denied`, check the `.pem` file permissions:

```bash
chmod 400 ~/keys/my-key.pem
```
