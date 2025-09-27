# 📱 Mphasis 企業打卡系統 - MDM 安裝指南

## 🎯 概述

本指南說明如何通過 iOS 的"VPN 与设备管理"功能安裝 Mphasis 企業打卡系統。

## 📋 安裝檔案說明

### 主要檔案：

1. **`configuration_profile.mobileconfig`** - iOS 配置文件
   - 包含 Web Clip 設定
   - VPN 配置（可選）
   - 企業安全策略

2. **`mobile_checkin_enterprise.html`** - 企業版打卡應用
   - 專為企業環境優化
   - MDM 管理功能
   - 增強安全性

3. **`mphasis_checkin.plist`** - 應用安裝清單
   - 用於原生應用安裝
   - 企業簽名支援

## 🚀 安裝方法

### 方法一：配置文件安裝（推薦）

#### 📤 **部署步驟：**

1. **上傳檔案到 HTTPS 伺服器**
   ```
   上傳檔案：
   - configuration_profile.mobileconfig
   - mobile_checkin_enterprise.html
   
   確保：
   - 使用 HTTPS 協議
   - 檔案可公開存取
   - 正確的 MIME 類型
   ```

2. **修改配置文件中的網址**
   ```xml
   <!-- 在 configuration_profile.mobileconfig 中修改 -->
   <key>URL</key>
   <string>https://your-server.com/mobile_checkin_enterprise.html</string>
   ```

3. **生成安裝連結**
   ```
   格式：itms-services://?action=download-manifest&url=https://your-server.com/configuration_profile.mobileconfig
   
   範例：itms-services://?action=download-manifest&url=https://company.com/mphasis/configuration_profile.mobileconfig
   ```

#### 📱 **用戶安裝步驟：**

1. **開啟安裝連結**
   - 用 Safari 開啟 `itms-services://` 連結
   - 或直接開啟 `.mobileconfig` 檔案

2. **確認安裝**
   - iOS 會提示「此網站正在嘗試下載設定描述檔」
   - 點擊「允許」

3. **安裝設定描述檔**
   - 前往「設定」→「一般」→「VPN 与设备管理」
   - 找到「Mphasis CheckIN 企業配置」
   - 點擊「安裝」

4. **輸入密碼確認**
   - 輸入設備密碼
   - 確認安裝

5. **完成安裝**
   - 主畫面會出現「Mphasis打卡」圖示
   - 可以像原生應用一樣使用

### 方法二：QR Code 安裝

#### 📊 **生成 QR Code：**

```
QR Code 內容：
itms-services://?action=download-manifest&url=https://your-server.com/configuration_profile.mobileconfig
```

#### 📱 **用戶掃描安裝：**

1. 用 iPhone 相機掃描 QR Code
2. 點擊彈出的通知
3. 按照上述安裝步驟操作

### 方法三：郵件/訊息分享

#### 📧 **分享安裝：**

1. **通過郵件**
   - 將 `.mobileconfig` 檔案作為附件
   - 用戶在 iPhone 上開啟附件

2. **通過訊息應用**
   - 分享安裝連結
   - 確保用 Safari 開啟

## ⚙️ 伺服器設定

### HTTPS 伺服器要求：

```nginx
# Nginx 配置範例
server {
    listen 443 ssl;
    server_name your-server.com;
    
    # SSL 憑證設定
    ssl_certificate /path/to/certificate.crt;
    ssl_certificate_key /path/to/private.key;
    
    location /mphasis/ {
        root /var/www/html;
        
        # 設定正確的 MIME 類型
        location ~ \.mobileconfig$ {
            add_header Content-Type application/x-apple-aspen-config;
        }
        
        location ~ \.plist$ {
            add_header Content-Type application/xml;
        }
        
        location ~ \.html$ {
            add_header Content-Type text/html;
        }
    }
}
```

### Apache 配置範例：

```apache
# .htaccess 檔案
AddType application/x-apple-aspen-config .mobileconfig
AddType application/xml .plist

# 啟用 HTTPS 重定向
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```

## 🔧 自訂設定

### 修改應用資訊：

```xml
<!-- 在 configuration_profile.mobileconfig 中 -->
<key>Label</key>
<string>您的公司打卡系統</string>

<key>URL</key>
<string>https://your-domain.com/your-app.html</string>
```

### 添加 VPN 設定：

```xml
<!-- VPN 配置區段 -->
<key>RemoteAddress</key>
<string>your-vpn-server.com</string>

<key>SharedSecret</key>
<string>your-vpn-secret</string>
```

### 自訂圖示：

1. 準備 180x180 像素的 PNG 圖示
2. 轉換為 Base64 編碼
3. 替換配置文件中的 `<data>` 區段

```bash
# 轉換圖示為 Base64
base64 -i icon-180x180.png -o icon-base64.txt
```

## 🛡️ 安全性考量

### 企業安全功能：

- ✅ **MDM 管理**：支援企業設備管理
- ✅ **證書驗證**：使用企業簽名證書
- ✅ **遠程配置**：支援遠程更新設定
- ✅ **使用追蹤**：記錄應用使用情況
- ✅ **安全策略**：強制企業安全政策

### 建議安全設定：

```xml
<!-- 安全限制設定 -->
<key>allowUIAppInstallation</key>
<true/>

<key>allowUntrustedTLSPrompt</key>
<false/>

<key>forceEncryptedBackup</key>
<true/>
```

## 📊 管理和監控

### 部署後管理：

1. **監控安裝狀況**
   - 檢查伺服器存取日誌
   - 統計下載次數

2. **用戶支援**
   - 提供安裝說明
   - 建立問題回報機制

3. **版本更新**
   - 更新 HTML 檔案
   - 推送新的配置文件

### 移除應用：

```
用戶可以通過以下方式移除：
1. 設定 → 一般 → VPN 与设备管理
2. 選擇配置文件
3. 點擊「移除描述檔」
```

## 🔍 故障排除

### 常見問題：

**❓ 安裝連結無效**
- 確認 HTTPS 協議
- 檢查檔案權限
- 驗證 MIME 類型

**❓ 無法下載配置文件**
- 確認網路連線
- 檢查伺服器狀態
- 驗證 SSL 憑證

**❓ 安裝後無法開啟**
- 檢查 HTML 檔案路徑
- 確認網址正確性
- 驗證檔案完整性

### 測試步驟：

1. **檢查檔案存取**
   ```bash
   curl -I https://your-server.com/configuration_profile.mobileconfig
   ```

2. **驗證 MIME 類型**
   ```bash
   curl -H "Accept: application/x-apple-aspen-config" https://your-server.com/configuration_profile.mobileconfig
   ```

3. **測試安裝連結**
   ```
   在 Safari 中開啟：
   itms-services://?action=download-manifest&url=https://your-server.com/configuration_profile.mobileconfig
   ```

## 📈 部署檢查清單

### 部署前：
- [ ] 準備 HTTPS 伺服器
- [ ] 修改檔案中的網址
- [ ] 設定正確的 MIME 類型
- [ ] 測試檔案存取

### 部署後：
- [ ] 測試安裝連結
- [ ] 驗證應用功能
- [ ] 檢查安全設定
- [ ] 建立用戶文檔

### 用戶培訓：
- [ ] 提供安裝說明
- [ ] 示範使用方法
- [ ] 建立支援管道
- [ ] 收集使用回饋

---

## 🎉 完成！

按照此指南，您可以成功通過 MDM 系統部署 Mphasis 企業打卡應用，提供員工方便且安全的打卡解決方案。

**技術支援：** 如需協助，請聯絡您的 IT 部門或系統管理員。
