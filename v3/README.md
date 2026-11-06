# ONNX v3 encoder weights

The encoder weights are stored as eight 7-Zip volumes. Extract them before loading the model. Keep all volumes together in `encoder-volumes/` and run these commands from `v3/`. The result must be named `encoder.onnx.data` and sit beside `encoder.onnx`.

The restored weights match the published ONNX INT4 encoder file. [Upstream file](https://huggingface.co/onnx-community/nemotron-3.5-asr-streaming-0.6b-onnx-int4/blob/ad805b70f4899a05d7d9979e2e7992be2fca3eb7/encoder.onnx.data)

## Windows PowerShell

Install 7-Zip and adjust `$seven` if needed. [7-Zip downloads](https://www.7-zip.org/download.html)

```powershell
$seven = "$env:ProgramFiles\7-Zip\7z.exe"
Get-Content -LiteralPath SHA256SUMS -ErrorAction Stop | ForEach-Object {
    $expected, $path = $_ -split '  ', 2
    $actual = (Get-FileHash -LiteralPath $path -Algorithm SHA256 -ErrorAction Stop).Hash
    if ($actual -ine $expected) { throw "SHA-256 mismatch for $path" }
}
& $seven x 'encoder-volumes/encoder.onnx.data.7z.001'
if ($LASTEXITCODE -ne 0) { throw '7-Zip extraction failed' }
$expected = (Get-Content -LiteralPath encoder.onnx.data.sha256 -ErrorAction Stop) -split '  ', 2
$actual = (Get-FileHash -LiteralPath encoder.onnx.data -Algorithm SHA256 -ErrorAction Stop).Hash
if ($actual -ine $expected[0]) { throw 'SHA-256 mismatch for encoder.onnx.data' }
Write-Output 'encoder.onnx.data SHA-256 verified'
```

## Linux and macOS

Use `7z` if your installation provides it in place of `7zz`. On macOS, replace both `sha256sum` invocations with `shasum -a 256`.

```sh
sha256sum -c SHA256SUMS &&
  7zz x encoder-volumes/encoder.onnx.data.7z.001 &&
  sha256sum -c encoder.onnx.data.sha256
```

See the [MIT license](LICENSE) and [third-party notices](NOTICES) for the model distribution.
