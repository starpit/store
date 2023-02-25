Download the latest release with the command:

```bash
which kubectl || (\
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/arm/kubectl" \
chmod +x kubectl \
sudo mv kubectl /usr/local/bin \
echo /usr/local/bin/kubectl \
)
```
