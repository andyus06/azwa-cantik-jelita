# Panduan Backup Data Linux Mint 22

## 1. Backup Menggunakan Timeshift (Backup Sistem)

Timeshift adalah tool backup bawaan Linux Mint untuk backup sistem dan pengaturan.

### Membuka Timeshift
```bash
sudo timeshift-gtk
```

### Langkah-langkah:
1. Buka **Menu** > **Administration** > **Timeshift**
2. Pilih jenis snapshot: **RSYNC** (untuk hard drive biasa) atau **BTRFS** (untuk filesystem BTRFS)
3. Pilih lokasi penyimpanan backup
4. Atur jadwal otomatis jika diperlukan
5. Klik **Create** untuk membuat snapshot

## 2. Backup Data Personal

### 2.1 Menggunakan rsync (Command Line)

**Backup folder Home ke external drive:**
```bash
rsync -avh --progress /home/username/ /media/username/BackupDrive/
```

**Backup dengan exclude tertentu:**
```bash
rsync -avh --progress --exclude='.cache' --exclude='Downloads' /home/username/ /media/username/BackupDrive/
```

### 2.2 Menggunakan Deja Dup (GUI)

1. Install Deja Dup:
```bash
sudo apt update
sudo apt install deja-dup
```

2. Buka **Menu** > **Preferences** > **Backups**
3. Pilih **Folders to save**
4. Pilih **Folders to ignore**
5. Pilih **Storage location**
6. Klik **Back Up Now**

## 3. Backup Menggunakan GUI Tools

### 3.1 Menggunakan File Manager (Nemo)
1. Buka **File Manager**
2. Pilih folder yang ingin dibackup
3. Klik kanan > **Copy**
4. Navigate ke lokasi backup
5. Klik kanan > **Paste**

### 3.2 Menggunakan Back In Time
```bash
sudo apt install backintime-qt
```

1. Buka **Back In Time**
2. Atur **Where to save snapshots**
3. Pilih **What to backup**
4. Atur **When to backup** (schedule)
5. Klik **Take Snapshot**

## 4. Backup Configuration Files dan Aplikasi

### Backup file konfigurasi penting:
```bash
# Membuat folder backup config
mkdir ~/backup-config

# Backup file konfigurasi sistem
cp -r ~/.config ~/backup-config/
cp -r ~/.local ~/backup-config/
cp ~/.bashrc ~/backup-config/
cp ~/.profile ~/backup-config/

# Backup daftar aplikasi terinstal
dpkg --get-selections > ~/backup-config/installed-packages.txt
snap list > ~/backup-config/snap-packages.txt
```

### Backup konfigurasi aplikasi khusus:
```bash
# Backup konfigurasi MariaDB
sudo cp /etc/mysql/mariadb.conf.d/50-server.cnf ~/backup-config/

# Backup konfigurasi MongoDB
sudo cp /etc/mongod.conf ~/backup-config/

# Backup konfigurasi VS Code
cp -r ~/.config/Code ~/backup-config/vscode-config/
```

## 5. Backup Database dan Aplikasi Khusus

### 5.1 Backup MariaDB:
```bash
# Backup semua database
mysqldump -u root -p --all-databases > ~/backup-config/mariadb-all-databases.sql

# Backup database tertentu
mysqldump -u root -p nama_database > ~/backup-config/nama_database.sql

# Backup struktur database saja (tanpa data)
mysqldump -u root -p --no-data nama_database > ~/backup-config/struktur_database.sql
```

### 5.2 Backup MongoDB:
```bash
# Backup semua database MongoDB
mongodump --out ~/backup-config/mongodb-backup/

# Backup database tertentu
mongodump --db nama_database --out ~/backup-config/mongodb-backup/

# Backup collection tertentu
mongodump --db nama_database --collection nama_collection --out ~/backup-config/mongodb-backup/
```

### 5.3 Backup Konfigurasi Visual Studio Code:
```bash
# Backup settings dan extensions VS Code
cp -r ~/.config/Code/User ~/backup-config/vscode-settings/

# Backup daftar extensions yang terinstall
code --list-extensions > ~/backup-config/vscode-extensions.txt
```

### 5.4 Restore Extensions VS Code:
```bash
# Install extensions dari backup
cat ~/backup-config/vscode-extensions.txt | xargs -L1 code --install-extension
```

## 6. Restore Database dan Aplikasi

### Restore MariaDB:
```bash
# Restore semua database
mysql -u root -p < ~/backup-config/mariadb-all-databases.sql

# Restore database tertentu
mysql -u root -p nama_database < ~/backup-config/nama_database.sql
```

### Restore MongoDB:
```bash
# Restore semua database
mongorestore ~/backup-config/mongodb-backup/

# Restore database tertentu
mongorestore --db nama_database ~/backup-config/mongodb-backup/nama_database/
```

### Restore VS Code settings:
```bash
# Restore settings VS Code
cp -r ~/backup-config/vscode-settings/* ~/.config/Code/User/

# Install extensions yang sudah dibackup
cat ~/backup-config/vscode-extensions.txt | xargs -L1 code --install-extension
```

## 7. Restore Sistem dan Data Personal

### Restore menggunakan Timeshift:
1. Boot dari Live USB Linux Mint
2. Install dan jalankan Timeshift
3. Pilih snapshot yang ingin direstore
4. Klik **Restore**

### Restore data personal:
```bash
rsync -avh --progress /media/username/BackupDrive/ /home/username/
```

### Strategi 3-2-1:
- **3** salinan data (1 original + 2 backup)
- **2** media penyimpanan berbeda
- **1** backup offsite/cloud

### Lokasi Backup yang Disarankan:
- External hard drive
- USB flash drive
- Network Attached Storage (NAS)
- Cloud storage (Google Drive, Dropbox, dll.)

### Jadwal Backup:
- **Harian**: File kerja penting
- **Mingguan**: Seluruh folder Home
- **Bulanan**: Full system backup

## 8. Automated Backup Script

Buat script otomatis:
```bash
#!/bin/bash
# backup-script.sh

DATE=$(date +%Y%m%d_%H%M%S)
SOURCE="/home/$USER"
DEST="/media/$USER/BackupDrive/backup_$DATE"

echo "Memulai backup ke $DEST"
rsync -avh --progress "$SOURCE" "$DEST"
echo "Backup selesai!"
```

Jalankan dengan:
```bash
chmod +x backup-script.sh
./backup-script.sh
```

### Mengatur C
