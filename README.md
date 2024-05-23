$OutlookBitness = Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Office\ClickToRun\Configuration" -Name "Platform" -ErrorAction SilentlyContinue

if ($OutlookBitness) {
    Write-Output "Outlook Platform: $($OutlookBitness.Platform)"
} else {
    $OutlookBitness = Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Office\$((Get-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Office' -Name 'Common' -ErrorAction SilentlyContinue).Path)\Outlook" -Name "Bitness" -ErrorAction SilentlyContinue

    if ($OutlookBitness) {
        Write-Output "Outlook Bitness: $($OutlookBitness.Bitness)"
    } else {
        Write-Output "Outlook bitness information not found."
    }
}
