
function Get-ADObjectAttribute
{
    [CmdletBinding()]
    Param
    (
        [Parameter(Mandatory=$true, 
                    ValueFromPipeline=$true,
                    ValueFromPipelineByPropertyName=$true,
                    ValueFromRemainingArguments=$false,
                    Position=0,
                    ParameterSetName='SET1')]
        [ValidateNotNull()]
        [ValidateNotNullOrEmpty()]
        [Alias("class")] 
        [String]$ObjectClass,
        [Parameter(Mandatory=$true, 
                    ValueFromPipeline=$true,
                    ValueFromPipelineByPropertyName=$true,
                    ValueFromRemainingArguments=$false,
                    Position=0,
                    ParameterSetName='SET1')]
        [ValidateNotNull()]
        [ValidateNotNullOrEmpty()]
        [String]$Property,
        [Parameter(Mandatory=$true, 
                    ValueFromPipeline=$true,
                    ValueFromPipelineByPropertyName=$true,
                    ValueFromRemainingArguments=$false,
                    Position=0,
                    ParameterSetName='SET1')]
        [ValidateNotNull()]
        [ValidateNotNullOrEmpty()]
        [Alias("val")]
        [String]$Value
    )
    $RootDSE = [ADSI]"LDAP://rootDSE"
    $RootDNS = $RootDSE.RootDomainNamingContext
    $Root=[ADSI]"GC://$RootDNS"
    $searcher = new-object System.DirectoryServices.DirectorySearcher($Root)
    $searcher.filter = "(&(objectClass=$ObjectClass)($Property=$Value))"
    $result = $searcher.findall()
    if($result) { Return ($result)}
    else{ Return $null }
}
