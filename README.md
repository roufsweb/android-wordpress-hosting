# 📱 Host a WordPress Website From Android with Your Own Domain via Cloudflare Tunnel

This guide walks you through hosting your **WordPress site directly from your Android phone**, using **Cloudflare Zero Trust Tunnels** and a **custom domain name**. You do not need root access. All steps are beginner-friendly and explained clearly.

---

## 🧰 What You'll Need

* 📱 Android phone (no root required)
* 🌐 Internet access (Wi-Fi recommended)
* 🔑 A domain name (from Namecheap, GoDaddy, Freenom, etc.)
* ☁️ A free [Cloudflare](https://cloudflare.com) account
* 📦 Apps:

  * **Termux** (from [F-Droid](https://f-droid.org))
  * **KSWeb** (from Google Play Store)

---

## 🔧 Step-by-Step Installation Guide

### 📌 Step 1: Purchase a Domain

Buy a domain from any registrar you prefer:

* [Namecheap](https://www.namecheap.com/)
* [GoDaddy](https://www.godaddy.com/)

After purchasing, **do not close the tab**, you'll need to update nameservers later.

---

### ☁️ Step 2: Set Up Cloudflare DNS

1. Go to [https://cloudflare.com](https://cloudflare.com)
2. Create a **free account** and log in.
3. In the Cloudflare dashboard, click **"Add a Site"** and enter your domain.
4. Select the **Free Plan**.
5. Cloudflare will give you **two nameservers** (e.g., `sue.ns.cloudflare.com`, `ivan.ns.cloudflare.com`).
6. Go back to your domain provider and:

   * Locate your domain's **nameserver/DNS settings**
   * Replace existing nameservers with the ones provided by Cloudflare
   * Save the changes (DNS propagation may take a few minutes to hours)

---

### 🔒 Step 3: Set Up Cloudflare Zero Trust Tunnel

1. In Cloudflare dashboard, click the hamburger icon (☰) and go to **Zero Trust**.
2. Click **Start for Free** and create a Zero Trust account.

   * You may be asked to enter a credit card for verification (you won't be charged).
3. Go to **Access > Tunnels > Create Tunnel**
4. Enter any tunnel name (e.g., `android-wordpress-tunnel`) and click **Save**

Leave the install screen open — we'll use it later.

---

## 📱 Android Configuration

### 🔨 Step 4: Install Required Apps

* **Install Termux**: Go to [F-Droid](https://f-droid.org) and install Termux
* **Install KSWeb**: Download from Google Play Store

### 🐧 Step 5: Install Ubuntu in Termux

Open Termux and copy-paste:

```bash
yes | pkg update && pkg install wget curl proot tar -y
wget https://raw.githubusercontent.com/AndronixApp/AndronixOrigin/master/Installer/Ubuntu22/ubuntu22.sh -O ubuntu22.sh
chmod +x ubuntu22.sh
bash ubuntu22.sh
```

Now you'll be inside the Ubuntu terminal.

Run the following to prepare the system:

```bash
apt update && apt upgrade -y
apt install sudo curl nano wget -y
```

### 🌩️ Step 6: Install Cloudflared in Ubuntu (inside Termux)

```bash
sudo mkdir -p --mode=0755 /usr/share/keyrings
curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg | sudo tee /usr/share/keyrings/cloudflare-main.gpg >/dev/null

echo 'deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared any main' | sudo tee /etc/apt/sources.list.d/cloudflared.list

sudo apt-get update && sudo apt-get install cloudflared -y
```

---

### 🌐 Step 7: Link Your Tunnel

1. In your Cloudflare **Zero Trust dashboard** → Your tunnel → "Install and run connector"
2. Choose:

   * **OS**: `Debian/Ubuntu`
   * **Architecture**: `ARM 64-bit`
3. Copy the full command (it should start with `sudo cloudflared service install ...`)
4. Paste and run it in your Ubuntu Termux terminal
5. Tunnel should now be active!

You can check its status from the Cloudflare dashboard.

---

## 🛠️ Hosting WordPress on Android

### 📂 Step 8: Prepare WordPress in KSWeb

1. Open KSWeb and start Apache server
2. Go to [https://wordpress.org/download/](https://wordpress.org/download/)
3. Download and extract the WordPress ZIP
4. Move the extracted contents to the `htdocs` folder in KSWeb:

---

### 🧰 Step 9: Setup phpMyAdmin and MySQL

1. In KSWeb tools, enable **phpMyAdmin** and **MySQL**
2. Navigate to:

```
http://localhost:with your port 
```

3. Login with:

   * **User**: `root`
   * **Password**: *(leave blank)*
4. Create a database named `wordpress`

---

### 🌐 Step 10: Map Domain to Local Server

1. In Cloudflare → Zero Trust → Tunnels → Your Tunnel
2. Under **Public Hostname**, click **Add**:

   * **Domain**: `yourdomain.com`
   * **Service**: `HTTP`
   * **URL**: `http://localhost:with your port`
3. Save

Now your domain should point to your Android-hosted WordPress site!

---

### 🧩 Step 11: Install WordPress

1. Open browser and go to:

```
https://yourdomain.com
```

2. Follow the WordPress installation wizard
3. Enter:

   * **DB Name**: `your created name `
   * **User**: `root`
   * **Password**: *(blank)*
4. Complete installation and start building your site 🎉

---

## 🔐 Bonus: Enable HTTPS (SSL)

Cloudflare provides free SSL. To make it work smoothly:

1. Install the plugin: [Really Simple SSL](https://wordpress.org/plugins/really-simple-ssl/)
2. Add to `wp-config.php`:

```php
define('FORCE_SSL_ADMIN', true);
if (strpos($_SERVER['HTTP_X_FORWARDED_PROTO'], 'https') !== false)
    $_SERVER['HTTPS']='on';
```

This will force SSL and prevent mixed content errors.

---

## 🚀 Done!

You’ve successfully hosted a WordPress website **on your Android device** using your **own domain** and **Cloudflare Tunnel**. No expensive hosting, no root, and you’re in full control.

---

## 🛠️ Troubleshooting & Tips

* To re-enter Ubuntu in Termux: `./start-ubuntu22.sh`
* You can restart the Cloudflare tunnel with: `sudo cloudflared tunnel run <TUNNEL_NAME>`
* Use lightweight WordPress themes for better phone performance
* Bookmark your phpMyAdmin and WordPress admin links

---

## 📝 Credits

* Ubuntu provisioning by [Andronix](https://github.com/AndronixApp)
* Cloudflared APT source from [Cloudflare Docs](https://pkg.cloudflare.com/)

---
