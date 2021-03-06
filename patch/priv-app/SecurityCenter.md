## 安全中心
APK位置： `/system/priv-app/SecurityCenter/SecurityCenter.apk`

apktool命令： `apktool d -r *.apk`

对 `IS_INTERNATIONAL_BUILD:Z` 统一处理的方法：将 `winter` 文件夹拷贝到 `smali/com` 目录下，将 `Lmiui/os/Build;->IS_INTERNATIONAL_BUILD:Z` 修改为 `Lcom/winter/mysu;->TRUE:Z`

### 移除网络助手主界面的『流量购买』条目
代码位置： `com/miui/networkassistant/ui/NetworkAssistantActivity.smali`
```
.method private checkTrafficPurchaseEnable
# 在某些 MIUI 版本中，该方法可能是 .method private checkTrafficPurchaseAvaliable
# 搜索 Lcom/miui/networkassistant/utils/DeviceUtil;->IS_INTERNATIONAL_BUILD:Z 将其改成 Lcom/winter/mysu;->TRUE:Z
```

### 移除网络助手的流量购买提醒
代码位置： `com/miui/networkassistant/config/SimUserInfo.smali`
```
.method public isPurchaseTipsEnable()Z
# return false
```
代码位置： `com/miui/networkassistant/provider/NetworkAssistantProvider.smali`
```
.method private queryDataUsageNotiStatus
# 将以下代码：
invoke-static {v4}, Lmiui/provider/ExtraNetwork;->isTrafficPurchaseSupported(Landroid/content/Context;)Z

move-result v2
# 修改为 sget-boolean v0, Lcom/winter/mysu;->FALSE:Z
```
代码位置： `com/miui/networkassistant/utils/LoadConfigUtil.smali`
```
.method public static isDataUsagePurchaseEnabled
# return false
```

### 移除网络诊断的一键WLAN测速功能
代码位置： `com/miui/networkassistant/ui/activity/NetworkDiagnosticsActivity.smali`
```
.method public onCreateOptionsMenu
# 搜索 Lcom/miui/networkassistant/utils/DeviceUtil;->IS_INTERNATIONAL_BUILD:Z 将其改成 Lcom/winter/mysu;->TRUE:Z
```

### 移除安全月报
代码路径： `com/miui/monthreport`
```
# 搜索 Lmiui/os/Build;->IS_INTERNATIONAL_BUILD:Z 将其改成 Lcom/winter/mysu;->TRUE:Z

# 同时也将 smali 中其它匹配 monthreport 的 Lmiui/os/Build;->IS_INTERNATIONAL_BUILD:Z 改成 Lcom/winter/mysu;->TRUE:Z
# 一般在 com/miui/push 目录
```

### 恢复应用权限监控&USB安装管理设置入口
代码位置： `com/miui/permcenter/MainAcitivty.smali`
```
.method protected onCreate
# 找到该方法中调用的布尔型函数()Z，在 com/miui/permcenter 路径查找对应方法，return ture
# 搜索代码 IS_STABLE_VERSION:Z 移除开发版自带的ROOT权限管理（对其 return true）
```

### 移除安全体检（立即优化）完成页的资讯推荐
代码路径： `com/miui/securityscan`
```
# 在该路径中查找：key_sc_setting_news_recommend ，此代码会在两个方法出现，对布尔值函数return false
# 等同于：反编译res，在 values/public.xml 找到 preference_key_information_setting_close 对应的id，再在 smali 中查找其对应的布尔值函数，return false
```

### 移除安全中心首页底部的推荐广告
代码位置：com/miui/securityscan/MainActivity.smali
```
# 搜索 Lmiui/os/Build;->IS_INTERNATIONAL_BUILD:Z 将其改成 Lcom/winter/mysu;->TRUE:Z
```

修改后：
```
.method private Gq()Z
    .locals 2

    sget-boolean v0, Lcom/winter/mysu;->TRUE:Z

    if-nez v0, :cond_0

    const-string/jumbo v0, "zh_CN" # 关键词："zh_CN"

    invoke-static {}, Ljava/util/Locale;->getDefault()Ljava/util/Locale;

    move-result-object v1

    invoke-virtual {v1}, Ljava/util/Locale;->toString()Ljava/lang/String;

    move-result-object v1

    invoke-virtual {v0, v1}, Ljava/lang/String;->equals(Ljava/lang/Object;)Z

    move-result v0

    :goto_0
    return v0

    :cond_0
    const/4 v0, 0x0

    goto :goto_0
.end method
```

### 移除安全体检不必要的扫描检测项
代码路径： `com/miui/securityscan/model/manualitem` 、`com/miui/securityscan/model/system`
```
.method public scan()V
# return null
```

### 移除安全体检的“系统更新自动下载检测”
代码位置： `com/miui/securityscan/model/system/AutoDownloadModel.smali`
```
.method public getDesc()Ljava/lang/String;
# 修改为：
.method public getDesc()Ljava/lang/String;
    .locals 1

    const/4 v0, 0x0

    return-object v0
.end method
# 注：须同时对 .method public scan()V 方法 return null
```

### 移除安全中心设置的信息流设置项

代码位置： `com/miui/securityscan/ui/settings/SettingsActivity.smali`
```
.method protected onCreate
# 在方法结束 return-void 前增加以下代码：
const-string/jumbo v0, "category_information_setting"

invoke-virtual {p0, v0}, Lcom/miui/securityscan/ui/settings/SettingsActivity;->findPreference(Ljava/lang/CharSequence;)Landroid/preference/Preference;

move-result-object v0

check-cast v0, Landroid/preference/PreferenceCategory;

invoke-virtual {p0}, Lcom/miui/securityscan/ui/settings/SettingsActivity;->getPreferenceScreen()Landroid/preference/PreferenceScreen;

move-result-object v1

invoke-virtual {v1, v0}, Landroid/preference/PreferenceScreen;->removePreference(Landroid/preference/Preference;)Z
```

### 移除安全扫描的系统更新检测
代码路径： `com/miui/antivirus`
```
.method protected onCreate
# 在该方法中搜索 preference_key_check_item_update 对应的 id（在 res/values/public.xml 可以找到）定位相关项
# 可以发现诸如以下的代码：
const v0, 0x7f0705ac # preference_key_check_item_update 对应的 id

invoke-virtual {p0, v0}, Lcom/miui/antivirus/activity/SettingsActivity;->getString(I)Ljava/lang/String;

move-result-object v0

invoke-virtual {p0, v0}, Lcom/miui/antivirus/activity/SettingsActivity;->findPreference(Ljava/lang/CharSequence;)Landroid/preference/Preference;

move-result-object v0

check-cast v0, Landroid/preference/CheckBoxPreference;

iput-object v0, p0, Lcom/miui/antivirus/activity/SettingsActivity;->avc:Landroid/preference/CheckBoxPreference;

# 在方法结束 return-void 前增加以下代码
# auQ 对应 preference_key_category_check_item, avc 对应 preference_key_check_item_update
# 在 res/values/public.xml 找到 preference_key_category_check_item 对应的 id, 然后在该方法中定位 preference_key_category_check_item
iget-object v0, p0, Lcom/miui/antivirus/activity/SettingsActivity;->auQ:Landroid/preference/PreferenceCategory;

iget-object v1, p0, Lcom/miui/antivirus/activity/SettingsActivity;->avc:Landroid/preference/CheckBoxPreference;

invoke-virtual {v0, v1}, Landroid/preference/PreferenceCategory;->removePreference(Landroid/preference/Preference;)Z
```

### 移除应用管理的资源推荐，关闭应用升级提醒
代码路径/位置： `com/miui/appmanager/model` 、`com/miui/appmanager/AppManagerMainActivity.smali`
```
# 搜索 Lmiui/os/Build;->IS_INTERNATIONAL_BUILD:Z 将其改成 Lcom/winter/mysu;->TRUE:Z
# ps：在 AppManagerMainActivity.smali 中有个存在关键代码 com.google.gms 的方法跳过不用修改
```
代码路径： `com/miui/appmanager`
```
# 搜索
am_update_app_notify 
am_ads_enable
# 对应的布尔值函数，return false
```

### 优化网络助手的通知样式
如果你在国内版MIUI使用国际版通知样式，请执行此修改，否则会错位显示网络助手的icon

代码路径： `com/miui/networkassistant/utils`
```
# 搜索代码 setIconRes 定位相关方法
# 将方法中的 Lcom/miui/networkassistant/utils/DeviceUtil;->IS_INTERNATIONAL_BUILD:Z
# 修改为 Lcom/winter/mysu;->TRUE:Z
```
