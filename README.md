# macOS on HP 250 G7 (6UK91EA)

OpenCore EFI for running macOS Sequoia on one specific laptop: **HP 250 G7, product number 6UK91EA**.

**[English](#en)** | **[Русский](#ru)**

---

<a name="en"></a>

## English

### ⚠️ Read this first

**This EFI is made for exactly one laptop configuration: HP 250 G7 6UK91EA (Intel Core i3-7020U, Intel HD Graphics 620).**

"HP 250 G7" is a whole family of laptops with very different hardware inside: 8th and 10th generation Intel processors, Pentium and Celeron chips, Realtek Wi-Fi cards. This EFI will **not** work on them: macOS will either not start at all or will run without graphics, sound or Wi-Fi.

Check the product number on the sticker on the bottom of your laptop. If it is not `6UK91EA`, do not use this EFI as is.

Also keep in mind:

- Installation **erases the whole internal disk**, including Windows and all your files. Back up everything you need first.
- You do everything at your own risk. Nobody guarantees that it will work or keep working after a macOS update.
- macOS is licensed by Apple for Apple computers only.

### Hardware

| Component | Details |
| --- | --- |
| Model | HP 250 G7, product number 6UK91EA |
| Processor | Intel Core i3-7020U (Kaby Lake) |
| Graphics | Intel HD Graphics 620 |
| Wi-Fi / Bluetooth | Intel card (driven by `itlwm` and `IntelBluetoothFirmware`) |
| Ethernet | Realtek RTL8111 |
| macOS | Sequoia |
| Mac model used (SMBIOS) | MacBookPro15,2 |

### What works

According to the author, everything works in daily use:

- Graphics acceleration, built-in display, brightness control and brightness keys
- Sound
- Wi-Fi (through the HeliPort app, see step 8) and Bluetooth
- Ethernet
- Keyboard and touchpad
- Battery status
- USB ports
- Sleep and wake

Not tested: AirDrop, Handoff and other Continuity features, iMessage and FaceTime. With Intel Wi-Fi cards on the `itlwm` driver, AirDrop and Handoff normally do not work.

### Installation, step by step

You will need:

- the HP 250 G7 6UK91EA laptop itself;
- a USB flash drive, 16 GB or larger (everything on it will be erased);
- a wired internet connection (Ethernet cable to your router): the installer downloads macOS from Apple, and Wi-Fi does not work inside the installer;
- any computer with Windows to prepare the flash drive (the laptop itself is fine);
- one to two hours.

**Step 1. Download this EFI.**
On this page click the green **Code** button, then **Download ZIP**. Unpack the archive. Inside you will find a folder named `EFI`.

**Step 2. Prepare the installer flash drive.**
Follow the official OpenCore guide, section [Making the installer in Windows](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/windows-install.html). Choose macOS Sequoia there. When you finish, the flash drive must contain a folder named `com.apple.recovery.boot`.

Stop after that section. You do not need the following chapters of the guide about building your own EFI: the ready one is in this repository.

**Step 3. Copy the EFI to the flash drive.**
Copy the whole `EFI` folder from step 1 to the root of the flash drive. The flash drive should now contain two folders side by side:

```
EFI
com.apple.recovery.boot
```

**Step 4. Set your own serial numbers.**
The file `EFI/OC/config.plist` in this repository contains placeholder serial numbers. macOS will start with them, but before you sign in with an Apple ID you must replace them with your own. Follow the section [PlatformInfo](https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/kaby-lake.html#platforminfo) of the OpenCore guide and generate numbers for the model **MacBookPro15,2**.

Never use someone else's serial numbers and never publish your own.

**Step 5. Check the BIOS settings.**
Turn the laptop on and immediately start pressing **F10** to open the BIOS setup. The usual requirements for OpenCore are:

- Secure Boot: disabled;
- Legacy Support: disabled (the laptop must boot in UEFI mode).

Save and exit. The author did not record their exact BIOS settings, so these are the general recommendations of the OpenCore guide.

**Step 6. Boot from the flash drive.**
Connect the Ethernet cable and the flash drive. Turn the laptop on and immediately start pressing **F9**. In the menu choose your flash drive.

Right after that **press and hold Esc**: the OpenCore boot menu is hidden in this EFI and only appears while Esc is held. In the menu choose the entry with the macOS installer (it may be called `macOS Base System` or `com.apple.recovery.boot`).

Lines of white text on a black screen will run for a while. This is normal.

The installer may start in German. You can change the language in the menu bar at the top of the screen.

**Step 7. Erase the disk and install macOS.**

1. Open **Disk Utility**.
2. In the menu **View** choose **Show All Devices**.
3. Select the internal disk (the top-level line, not a partition under it) and click **Erase**. Format: **APFS**. Scheme: **GUID Partition Map**. This deletes everything on the disk.
4. Close Disk Utility and choose **Reinstall macOS Sequoia**. Select the disk you just erased.

The laptop will restart several times. Each time press **F9**, choose the flash drive, hold **Esc**, and pick the entry `macOS Installer`. When that entry is gone, pick the entry with the name you gave your disk. At the end the macOS setup assistant will open.

**Step 8. Turn on Wi-Fi.**
Download the [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) app and move it to the Applications folder. It adds a Wi-Fi icon to the menu bar where you choose a network. Add HeliPort to your login items so it starts automatically.

**Step 9. Make the laptop boot without the flash drive.**
Until now macOS starts only with the flash drive plugged in. To fix that, copy the `EFI` folder from the flash drive to the hidden EFI partition of the internal disk. The easiest tool is [MountEFI](https://github.com/corpnewt/MountEFI): run it, choose the internal disk, and copy the `EFI` folder to the volume named `EFI` that appears.

After that, remove the flash drive and restart. macOS should boot by itself.

### Good to know

- **Boot menu is hidden.** To see the OpenCore menu, hold **Esc** while the laptop starts.
- **Text during startup.** The EFI boots in verbose mode (`-v`). To get the usual Apple logo, remove `-v` from `boot-args` in `config.plist`.
- **Security settings are lowered.** System Integrity Protection is partially disabled (`csr-active-config` = `03080000`) and the boot argument `amfi=0x80` is set. This is how the author's system is configured. If you do not need it, restore the defaults using the OpenCore guide.
- **macOS updates** can break a Hackintosh. Before a major update, check that newer versions of OpenCore and the kexts exist, and keep the installer flash drive as a rescue tool.
- **The previous version** of this EFI is available under the tag [`v1-old`](../../tree/v1-old).

### What is inside

Boot arguments: `keepsyms=1 -v alcid=3 applbkl=1 amfi=0x80 ipc_control_port_options=0`

ACPI tables: `SSDT-EC`, `SSDT-PLUG`, `SSDT-PNLF`, `SSDT-RTC`, `SSDT-USBX`

Drivers: `OpenRuntime`, `HfsPlus`, `OpenCanopy`, `AudioDxe`, `Ps2KeyboardDxe`, `Ps2MouseDxe`, `ResetNvramEntry`, `ToggleSipEntry`

| Kext | Version | Purpose |
| --- | --- | --- |
| Lilu | 1.7.2 | Base for other kexts |
| VirtualSMC | 1.3.8 | Emulates the Apple SMC chip |
| SMCBatteryManager | 1.3.8 | Battery status |
| WhateverGreen | 1.7.1 | Graphics |
| AppleALC | 1.9.8 | Sound |
| itlwm | 2.3.0 | Intel Wi-Fi (works with HeliPort) |
| IntelBluetoothFirmware | — | Intel Bluetooth firmware |
| BlueToolFixup | 2.7.2 | Intel Bluetooth support in recent macOS |
| RealtekRTL8111 | 3.0.0 | Ethernet |
| VoodooPS2Controller | 2.3.7 | Keyboard |
| VoodooRMI | 1.4.3 | Touchpad |
| BrightnessKeys | 1.0.3 | Brightness keys |
| ECEnabler | 1.0.6 | Battery readings from the embedded controller |
| CPUFriend | 1.3.0 | Processor power management |
| CPUFriendDataProvider | 1.0.1 | Power management data for CPUFriend |
| NVMeFix | 1.1.3 | NVMe drive power management |
| CtlnaAHCIPort | 341.0.2 | SATA controller |
| RestrictEvents | 1.1.6 | Hides irrelevant system warnings |
| USBMap | 1.0 | USB port map |

The folder also contains kexts that are switched off in `config.plist`: `AirportItlwm`, `IntelBTPatcher`, `SMCProcessor`, `SMCSuperIO`, `USBPorts`.

### Credits

- [Acidanthera](https://github.com/acidanthera) for OpenCore and most of the kexts
- [Dortania](https://dortania.github.io/OpenCore-Install-Guide/) for the OpenCore Install Guide
- [OpenIntelWireless](https://github.com/OpenIntelWireless) for the Intel Wi-Fi and Bluetooth drivers

---

<a name="ru"></a>

## Русский

### ⚠️ Сначала прочитайте

**Эта EFI сделана строго под одну конфигурацию ноутбука: HP 250 G7 6UK91EA (Intel Core i3-7020U, Intel HD Graphics 620).**

«HP 250 G7» — это целое семейство ноутбуков с очень разной начинкой: процессоры Intel 8-го и 10-го поколений, Pentium и Celeron, Wi-Fi-карты Realtek. На них эта EFI работать **не будет**: macOS либо не запустится вовсе, либо останется без графики, звука или Wi-Fi.

Посмотрите номер продукта на наклейке на днище ноутбука. Если там не `6UK91EA`, не используйте эту EFI как есть.

Также имейте в виду:

- Установка **стирает весь внутренний диск**, включая Windows и все ваши файлы. Сначала сохраните всё нужное.
- Вы всё делаете на свой страх и риск. Никто не гарантирует, что система заработает и не сломается после обновления macOS.
- По лицензии Apple macOS предназначена только для компьютеров Apple.

### Железо

| Компонент | Описание |
| --- | --- |
| Модель | HP 250 G7, номер продукта 6UK91EA |
| Процессор | Intel Core i3-7020U (Kaby Lake) |
| Графика | Intel HD Graphics 620 |
| Wi-Fi / Bluetooth | карта Intel (работает через `itlwm` и `IntelBluetoothFirmware`) |
| Ethernet | Realtek RTL8111 |
| macOS | Sequoia |
| Модель Mac (SMBIOS) | MacBookPro15,2 |

### Что работает

По словам автора, в повседневной работе работает всё:

- ускорение графики, встроенный экран, регулировка яркости и клавиши яркости
- звук
- Wi-Fi (через приложение HeliPort, см. шаг 8) и Bluetooth
- Ethernet
- клавиатура и тачпад
- индикатор батареи
- USB-порты
- сон и пробуждение

Не проверялось: AirDrop, Handoff и другие функции Continuity, iMessage и FaceTime. С Wi-Fi-картами Intel на драйвере `itlwm` AirDrop и Handoff обычно не работают.

### Установка по шагам

Понадобится:

- сам ноутбук HP 250 G7 6UK91EA;
- флешка на 16 ГБ или больше (всё с неё будет стёрто);
- проводной интернет (кабель Ethernet до роутера): установщик скачивает macOS с серверов Apple, а Wi-Fi внутри установщика не работает;
- любой компьютер с Windows, чтобы подготовить флешку (подойдёт и сам ноутбук);
- один-два часа времени.

**Шаг 1. Скачайте эту EFI.**
На этой странице нажмите зелёную кнопку **Code**, затем **Download ZIP**. Распакуйте архив. Внутри будет папка `EFI`.

**Шаг 2. Подготовьте установочную флешку.**
Следуйте официальному руководству OpenCore, раздел [Making the installer in Windows](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/windows-install.html) (на английском). Выбирайте там macOS Sequoia. В итоге на флешке должна появиться папка `com.apple.recovery.boot`.

После этого раздела остановитесь. Следующие главы руководства о сборке собственной EFI вам не нужны: готовая лежит в этом репозитории.

**Шаг 3. Скопируйте EFI на флешку.**
Скопируйте папку `EFI` из шага 1 целиком в корень флешки. Теперь на флешке должны лежать рядом две папки:

```
EFI
com.apple.recovery.boot
```

**Шаг 4. Пропишите свои серийные номера.**
В файле `EFI/OC/config.plist` из этого репозитория стоят серийные номера-заглушки. macOS с ними запустится, но до входа в Apple ID их нужно заменить своими. Следуйте разделу [PlatformInfo](https://dortania.github.io/OpenCore-Install-Guide/config-laptop.plist/kaby-lake.html#platforminfo) руководства OpenCore и сгенерируйте номера для модели **MacBookPro15,2**.

Никогда не используйте чужие серийные номера и не публикуйте свои.

**Шаг 5. Проверьте настройки BIOS.**
Включите ноутбук и сразу начните нажимать **F10**, чтобы войти в настройки BIOS. Обычные требования OpenCore:

- Secure Boot: выключен;
- Legacy Support: выключен (ноутбук должен загружаться в режиме UEFI).

Сохраните настройки и выйдите. Автор не записывал свои точные настройки BIOS, поэтому здесь приведены общие рекомендации из руководства OpenCore.

**Шаг 6. Загрузитесь с флешки.**
Подключите кабель Ethernet и флешку. Включите ноутбук и сразу начните нажимать **F9**. В меню выберите свою флешку.

Сразу после этого **нажмите и держите Esc**: меню загрузки OpenCore в этой EFI скрыто и появляется, только пока удерживается Esc. В меню выберите пункт с установщиком macOS (он может называться `macOS Base System` или `com.apple.recovery.boot`).

Некоторое время по чёрному экрану будут бежать строки белого текста. Это нормально.

Установщик может запуститься на немецком языке. Язык меняется в строке меню вверху экрана.

**Шаг 7. Сотрите диск и установите macOS.**

1. Откройте **Дисковую утилиту** (Disk Utility).
2. В меню **Вид** (View) выберите **Показывать все устройства** (Show All Devices).
3. Выберите внутренний диск (самую верхнюю строку, а не раздел под ней) и нажмите **Стереть** (Erase). Формат: **APFS**. Схема: **Схема разделов GUID**. Это удалит всё на диске.
4. Закройте Дисковую утилиту и выберите **Переустановить macOS Sequoia**. Укажите только что стёртый диск.

Ноутбук несколько раз перезагрузится. Каждый раз нажимайте **F9**, выбирайте флешку, держите **Esc** и выбирайте пункт `macOS Installer`. Когда этот пункт исчезнет, выбирайте пункт с именем, которое вы дали диску. В конце откроется помощник настройки macOS.

**Шаг 8. Включите Wi-Fi.**
Скачайте приложение [HeliPort](https://github.com/OpenIntelWireless/HeliPort/releases) и перенесите его в папку «Программы». Оно добавляет в строку меню значок Wi-Fi, через который выбирается сеть. Добавьте HeliPort в объекты входа, чтобы оно запускалось автоматически.

**Шаг 9. Сделайте так, чтобы ноутбук загружался без флешки.**
Пока что macOS запускается только со вставленной флешкой. Чтобы это исправить, скопируйте папку `EFI` с флешки на скрытый раздел EFI внутреннего диска. Проще всего через утилиту [MountEFI](https://github.com/corpnewt/MountEFI): запустите её, выберите внутренний диск и скопируйте папку `EFI` на появившийся том с названием `EFI`.

После этого выньте флешку и перезагрузитесь. macOS должна загрузиться сама.

### Полезно знать

- **Меню загрузки скрыто.** Чтобы увидеть меню OpenCore, держите **Esc** при включении ноутбука.
- **Текст при запуске.** EFI загружается в подробном режиме (`-v`). Чтобы вернуть обычный логотип Apple, уберите `-v` из `boot-args` в `config.plist`.
- **Защита системы ослаблена.** System Integrity Protection частично отключена (`csr-active-config` = `03080000`), и задан аргумент загрузки `amfi=0x80`. Так настроена система автора. Если вам это не нужно, верните значения по умолчанию по руководству OpenCore.
- **Обновления macOS** могут сломать хакинтош. Перед крупным обновлением проверьте, вышли ли новые версии OpenCore и кекстов, и держите установочную флешку под рукой как средство восстановления.
- **Предыдущая версия** этой EFI доступна по тегу [`v1-old`](../../tree/v1-old).

### Что внутри

Аргументы загрузки: `keepsyms=1 -v alcid=3 applbkl=1 amfi=0x80 ipc_control_port_options=0`

Таблицы ACPI: `SSDT-EC`, `SSDT-PLUG`, `SSDT-PNLF`, `SSDT-RTC`, `SSDT-USBX`

Драйверы: `OpenRuntime`, `HfsPlus`, `OpenCanopy`, `AudioDxe`, `Ps2KeyboardDxe`, `Ps2MouseDxe`, `ResetNvramEntry`, `ToggleSipEntry`

| Кекст | Версия | Назначение |
| --- | --- | --- |
| Lilu | 1.7.2 | основа для остальных кекстов |
| VirtualSMC | 1.3.8 | эмуляция чипа Apple SMC |
| SMCBatteryManager | 1.3.8 | состояние батареи |
| WhateverGreen | 1.7.1 | графика |
| AppleALC | 1.9.8 | звук |
| itlwm | 2.3.0 | Wi-Fi Intel (работает вместе с HeliPort) |
| IntelBluetoothFirmware | — | прошивка Bluetooth Intel |
| BlueToolFixup | 2.7.2 | поддержка Bluetooth Intel в новых macOS |
| RealtekRTL8111 | 3.0.0 | Ethernet |
| VoodooPS2Controller | 2.3.7 | клавиатура |
| VoodooRMI | 1.4.3 | тачпад |
| BrightnessKeys | 1.0.3 | клавиши яркости |
| ECEnabler | 1.0.6 | чтение данных батареи из контроллера |
| CPUFriend | 1.3.0 | управление питанием процессора |
| CPUFriendDataProvider | 1.0.1 | данные питания для CPUFriend |
| NVMeFix | 1.1.3 | управление питанием NVMe-диска |
| CtlnaAHCIPort | 341.0.2 | SATA-контроллер |
| RestrictEvents | 1.1.6 | скрывает лишние системные предупреждения |
| USBMap | 1.0 | карта USB-портов |

В папке также лежат кексты, отключённые в `config.plist`: `AirportItlwm`, `IntelBTPatcher`, `SMCProcessor`, `SMCSuperIO`, `USBPorts`.

### Благодарности

- [Acidanthera](https://github.com/acidanthera) — OpenCore и большинство кекстов
- [Dortania](https://dortania.github.io/OpenCore-Install-Guide/) — руководство OpenCore Install Guide
- [OpenIntelWireless](https://github.com/OpenIntelWireless) — драйверы Wi-Fi и Bluetooth Intel
