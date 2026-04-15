# NucleoMouse2023 ターミナル運用メモ（macOS / Windows）

このメモは、`NucleoMouse2023` を **CubeMXでコード生成**し、
**VSCode + ターミナルでビルド・書き込み**するための手順です。

- 対象MCU: `STM32F303K8`（NUCLEO-F303K8）
- ビルド方式: CubeIDE Managed Build のヘッドレス実行
- 書き込み方式: `STM32_Programmer_CLI`（SWD / ST-LINK）

> 補足: `nightfall-fw` と同じ「ターミナル中心運用」は可能です。  
> ただし本プロジェクトは現状 CMake 化していないため、`cmake --preset` ではなく
> CubeIDE ヘッドレスビルドを使います。

---

## 1. 事前準備（共通）

1. `STM32CubeIDE` をインストール
2. `STM32CubeProgrammer` をインストール
3. `NucleoMouse2023` を clone または ZIP 展開
4. VSCode で `NucleoMouse2023` フォルダを開く

---

## 2. CubeMXでコード生成（共通）

1. `NucleoMouse2023.ioc` を開く
2. 必要な設定を変更
3. `Generate Code` を実行

`Keep User Code` を有効のまま使ってください。

---

## 3. macOS 手順

### 3.1 実行ファイルの場所（例）

- CubeIDE:  
  `/Applications/STM32CubeIDE.app/Contents/MacOS/STM32CubeIDE`
- Programmer CLI:  
  `/Applications/STMicroelectronics/STM32Cube/STM32CubeProgrammer/STM32CubeProgrammer.app/Contents/MacOs/bin/STM32_Programmer_CLI`

### 3.2 ビルド（ヘッドレス）

```bash
"/Applications/STM32CubeIDE.app/Contents/MacOS/STM32CubeIDE" \
  -nosplash \
  -data "$HOME/.cubeide-headless" \
  -application org.eclipse.cdt.managedbuilder.core.headlessbuild \
  -import "/ABS/PATH/TO/NucleoMouse2023" \
  -cleanBuild NucleoMouse2023/Debug \
  -verbose
```

ビルド成果物:

- `Debug/NucleoMouse2023.elf`
- `Debug/NucleoMouse2023.bin`
- `Debug/NucleoMouse2023.hex`

### 3.3 書き込み（SWD）

```bash
"/Applications/STMicroelectronics/STM32Cube/STM32CubeProgrammer/STM32CubeProgrammer.app/Contents/MacOs/bin/STM32_Programmer_CLI" \
  -c port=SWD \
  -d "/ABS/PATH/TO/NucleoMouse2023/Debug/NucleoMouse2023.elf" \
  -v -rst
```

---

## 4. Windows 手順

### 4.1 実行ファイルの場所（例）

環境によりバージョン番号は変わります。

- CubeIDE（どちらかを使用）
  - `C:\ST\STM32CubeIDE_*/STM32CubeIDE/stm32cubeidec.exe`
  - `C:\ST\STM32CubeIDE_*/STM32CubeIDE/STM32CubeIDE.exe`
- Programmer CLI
  - `C:\Program Files\STMicroelectronics\STM32Cube\STM32CubeProgrammer\bin\STM32_Programmer_CLI.exe`

### 4.2 ビルド（PowerShell 例）

```powershell
& "C:\ST\STM32CubeIDE_1.18.1\STM32CubeIDE\stm32cubeidec.exe" `
  -nosplash `
  -data "$env:USERPROFILE\.cubeide-headless" `
  -application org.eclipse.cdt.managedbuilder.core.headlessbuild `
  -import "C:\ABS\PATH\TO\NucleoMouse2023" `
  -cleanBuild NucleoMouse2023/Debug `
  -verbose
```

### 4.3 書き込み（PowerShell 例）

```powershell
& "C:\Program Files\STMicroelectronics\STM32Cube\STM32CubeProgrammer\bin\STM32_Programmer_CLI.exe" `
  -c port=SWD `
  -d "C:\ABS\PATH\TO\NucleoMouse2023\Debug\NucleoMouse2023.elf" `
  -v -rst
```

---

## 5. よくある詰まりポイント

1. `-import` のパスは **絶対パス**を使う
2. `-cleanBuild NucleoMouse2023/Debug` の `NucleoMouse2023` は `.project` のプロジェクト名に一致させる
3. `STM32_Programmer_CLI` が見つからない場合は、フルパス指定で実行する
4. ST-LINK認識不良時は、USB差し直し → 再実行
5. 生成コード更新後は毎回 `-cleanBuild` を推奨

---

## 6. 運用メモ

- `nightfall-fw` の `tools/flashing/flash_uart` は現状 `STM32F405/F413` 向けです。
- `NucleoMouse2023 (STM32F303)` はこのメモの `STM32_Programmer_CLI` 手順で運用してください。
