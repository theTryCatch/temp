using System;
using System.Diagnostics;
using Microsoft.AspNetCore.Mvc;

namespace LdapSearch.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class LdapSearchController : ControllerBase
    {
        [HttpGet]
        public IActionResult Search(string query)
        {
            if (string.IsNullOrEmpty(query))
            {
                return BadRequest("Query parameter is required.");
            }

            try
            {
                // Execute the ldapsearch command
                var processStartInfo = new ProcessStartInfo
                {
                    FileName = "ldapsearch",
                    Arguments = $"-x -b 'dc=example,dc=com' '{query}'",
                    RedirectStandardOutput = true,
                    RedirectStandardError = true,
                    UseShellExecute = false,
                    CreateNoWindow = true
                };

                using (var process = Process.Start(processStartInfo))
                {
                    if (process == null)
                    {
                        return StatusCode(500, "Failed to start process.");
                    }

                    // Read the output
                    string output = process.StandardOutput.ReadToEnd();
                    string error = process.StandardError.ReadToEnd();

                    process.WaitForExit();

                    if (!string.IsNullOrEmpty(error))
                    {
                        return StatusCode(500, $"Error executing ldapsearch: {error}");
                    }

                    return Ok(output);
                }
            }
            catch (Exception ex)
            {
                return StatusCode(500, $"An error occurred: {ex.Message}");
            }
        }
    }
}
