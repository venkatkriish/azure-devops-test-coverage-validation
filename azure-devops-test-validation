using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)
$buildId = $Request.Body.buildId
$releaseId = $Request.Body.releaseId
$environmentId = $Request.Body.environmentId
$buildId
$orgId = $Request.Body.Org_Id
$code_percent = $Request.Body.codeCoverage
$test_percent = $Request.Body.testCoverage
$projectname = $Request.Body.projectName
write-host "test_percent $test_percent"
write-host "code_percent $code_percent"
$projectname
# Write to the Azure Functions log stream.
$User_PAT='$PAT'
$get_test_url = "https://dev.azure.com/$ORGID/$projectname/_apis/test/runs?includeRunDetails=true&api-version=5.1"
$get_code_url = "https://dev.azure.com/$ORGID/$projectname/_apis/test/codecoverage?buildId=$buildId&api-version=5.1-preview.1"
$cancel_release = [uri]::UnEscapeDataString("https://vsrm.dev.azure.com/tcotfs/$projectname/_apis/Release/releases/$releaseId/environments/$environmentId%3Fapi-version=6.1-preview.7")
$cancel_release 
$token1 = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("PAT:$User_PAT"))
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Basic $token1")
$pass = "false"
################################################################
### Get Test and code coverage results.
################################################################
$testCoverageOutput = Invoke-RestMethod -Uri $get_test_url -Headers $headers
$codeCoverageOutput = Invoke-RestMethod -Uri $get_code_url -Method 'GET' -Headers $headers

$x= $testCoverageOutput
################################################################
### Calculate Test coverage percentage.
################################################################
$coveringRange = $codeCoverageOutput.coverageData
$testcoverage_value = $testCoverageOutput.value | where { $_.build.Id -eq $buildId }
write-host "$testcoverage_value[0]"
$total=0
$total=$testcoverage_value[0].totalTests - $testcoverage_value[0].unanalyzedTests - $testcoverage_value[0].incompleteTests - $testcoverage_value[0].notApplicableTests
$testcoverage_value[0].totalTests
$percentage=$testcoverage_value[0].passedTests/$total*100

write-host "percentage $percentage"

################################################################
### get code coverage percentage.
################################################################
$branch_coveringRange = $coveringRange.coverageStats | where { $_.label -eq "Branches" }
$code_result = 0
$code_result = $branch_coveringRange.total
write-host "$code_result -ge $code_percent"
write-host "$percentage -ge $test_percent"
write-host "code_result $code_result"
If ($code_result -ne 0 -and $code_result -and $percentage -and $code_result -ge $code_percent -and $percentage -ge $test_percent){
    $pass = "true"
    write-host "overall result $pass"
}
write-host "not !$pass"
################################################################
### Cancel current Stage in release pipeline.
################################################################
If ( $pass -eq "false" ){
    write-host "failed test"
    $headers.Add("Content-Type", "application/json")
    $body = @{ status = 'canceled'; comment = 'fail due to test/code coverage failure' } | ConvertTo-Json
    Invoke-RestMethod -Uri $cancel_release -Method PATCH -Body $body -Headers $headers
}
$params = @"
{
"success": "$pass",
"testCoverage": "$percentage",
"codecoverage": "$code_result",
"message": ""
}
"@
$params

# Associate values to output bindings by calling 'Push-OutputBinding'.
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = [HttpStatusCode]::OK
    Body = $params

})
