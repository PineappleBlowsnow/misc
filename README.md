# NVIDIA Nemotron 3.5 ASR Streaming 0.6B

This repository contains the original NVIDIA NeMo checkpoint as 26 uncompressed 7-Zip volumes in `volumes/`. The restored file is `nemotron-3.5-asr-streaming-0.6b.nemo` (2,368,284,501 bytes). Its SHA-256 matches the one published by NVIDIA. [Upstream checkpoint](https://huggingface.co/nvidia/nemotron-3.5-asr-streaming-0.6b/blob/b0da14f/nemotron-3.5-asr-streaming-0.6b.nemo)

The model is distributed under the [OpenMDW 1.1 license](LICENSE).

## Restore and verify

Clone the repository, then run the commands below from its root. Keep all 26 volumes together in `volumes/`. 7-Zip reads the remaining volumes automatically when you open `.001`.

```sh
git clone https://github.com/PineappleBlowsnow/misc.git
cd misc
```

### Windows PowerShell

Install 7-Zip and adjust `$seven` if it is installed in a different location. [7-Zip downloads](https://www.7-zip.org/download.html)

```powershell
$seven = "$env:ProgramFiles\7-Zip\7z.exe"
$model = 'nemotron-3.5-asr-streaming-0.6b.nemo'
Get-Content -LiteralPath SHA256SUMS -ErrorAction Stop | ForEach-Object {
    $expected, $path = $_ -split '  ', 2
    $actual = (Get-FileHash -LiteralPath $path -Algorithm SHA256 -ErrorAction Stop).Hash
    if ($actual -ine $expected) { throw "SHA-256 mismatch for $path" }
}
& $seven x "volumes/$model.7z.001"
if ($LASTEXITCODE -ne 0) { throw '7-Zip extraction failed' }
$actual = (Get-FileHash -LiteralPath $model -Algorithm SHA256 -ErrorAction Stop).Hash
if ($actual -ine '210214ed94039bf6bfbb9a047c7fa289628db75b103e2bf6381fa78285436a74') {
    throw "SHA-256 mismatch for $model"
}
Write-Output "$model SHA-256 verified"
```

### Linux and macOS

Use `7zz` from 7-Zip. If your installation provides `7z`, use that command in its place. On macOS, replace both `sha256sum` invocations with `shasum -a 256`.

```sh
model=nemotron-3.5-asr-streaming-0.6b.nemo
sha256sum -c SHA256SUMS &&
  7zz x "volumes/$model.7z.001" &&
  printf '%s  %s\n' '210214ed94039bf6bfbb9a047c7fa289628db75b103e2bf6381fa78285436a74' "$model" | sha256sum -c
```
