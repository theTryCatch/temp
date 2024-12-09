function Convert-Pkcs1ToXml {
    param (
        [string]$pemFilePath
    )

    # Helper function to read and decode PEM files
    function Parse-Pem {
        param ([string]$path)
        $content = Get-Content $path | Where-Object {
            $_ -notmatch "-----BEGIN.*KEY-----" -and $_ -notmatch "-----END.*KEY-----"
        }
        return [Convert]::FromBase64String(($content -join ''))
    }

    # Parse the PEM file
    $pemBytes = Parse-Pem -path $pemFilePath

    # Check if it's a PKCS#1 key (starts with 0x30)
    if ($pemBytes[0] -ne 0x30) {
        throw "Invalid PKCS#1 format. Ensure the key is in PKCS#1 (BEGIN RSA PRIVATE KEY)."
    }

    # Manually extract components from the ASN.1 structure
    # Note: This is a simplified approach and assumes a valid RSA private key
    $index = 0
    function Read-Integer {
        param ([ref]$bytes, [ref]$index)
        $length = $bytes.Value[$index.Value + 1]
        $integer = $bytes.Value[($index.Value + 2)..($index.Value + 1 + $length)]
        $index.Value += 2 + $length
        return $integer
    }

    # Skip sequence header
    $index += 1
    $version = Read-Integer ([ref]$pemBytes) ([ref]$index) # Skip version
    $modulus = Read-Integer ([ref]$pemBytes) ([ref]$index)
    $exponent = Read-Integer ([ref]$pemBytes) ([ref]$index)
    $d = Read-Integer ([ref]$pemBytes) ([ref]$index)
    $p = Read-Integer ([ref]$pemBytes) ([ref]$index)
    $q = Read-Integer ([ref]$pemBytes) ([ref]$index)
    $dp = Read-Integer ([ref]$pemBytes) ([ref]$index)
    $dq = Read-Integer ([ref]$pemBytes) ([ref]$index)
    $inverseQ = Read-Integer ([ref]$pemBytes) ([ref]$index)

    # Construct the XML representation
    $xmlKey = @"
<RSAKeyValue>
  <Modulus>$([Convert]::ToBase64String($modulus))</Modulus>
  <Exponent>$([Convert]::ToBase64String($exponent))</Exponent>
  <D>$([Convert]::ToBase64String($d))</D>
  <P>$([Convert]::ToBase64String($p))</P>
  <Q>$([Convert]::ToBase64String($q))</Q>
  <DP>$([Convert]::ToBase64String($dp))</DP>
  <DQ>$([Convert]::ToBase64String($dq))</DQ>
  <InverseQ>$([Convert]::ToBase64String($inverseQ))</InverseQ>
</RSAKeyValue>
"@

    return $xmlKey
}

# Path to your PEM file
$pemFilePath = "C:\path\to\private-key.pem"

# Convert the PEM key to XML
try {
    $xmlKey = Convert-Pkcs1ToXml -pemFilePath $pemFilePath
    $outputPath = "C:\path\to\private-key.xml"
    $xmlKey | Set-Content -Path $outputPath
    Write-Output "Successfully converted PEM to XML format at $outputPath"
} catch {
    Write-Error "Failed to convert key: $_"
}
