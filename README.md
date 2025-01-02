######Apple Passkit#######

NOTE: you will use the php-pkpass library so its important to handle the certificates according to the instructions of the library 

#######Project Setup#####
1) Create an Apple Developer Account for your organization
2) Go to Certificates, IDs and Profiles Menu
3) Click plus button in Certificates+ (plus button)
4) Add a Pass Type ID and name it. 
5) Put a description and name for your pass identifier. This will start with te word "pass" eg. pass.test01. You will need this later
6) Open keychain access in your mac machine and click certificate assistant
7) Under certificate assistant, generate a certificate. This will be a SignerCert File
8) Go back to your apple developer caccount console, create a certificate for you pass ID. This will prompt you to upload the SignerCert File. Leave the name blank when downloading
9) Hit download and it should give you "pass.cer" file
10) Drag the "pass cer" file to keychain access
11) You will see the certificate fle along with the pass type name. CLick that and a private key should show below it
12) Select both of them and click export 2 items
13) This export will prompt you to save another file called Certificates.p12. This is the file youre gonna use in your code later on 
14) Run the commands below to generate the key_new.p12

####### Code Structure #######


1) Add the p12 file in root directory


 $passJson = [

            "formatVersion" => 1,
            "passTypeIdentifier" => "pass.[passname]",
            "serialNumber" => $serialNumber,
            "teamIdentifier" => "Your Team Identifier",
            "organizationName" => "Your Organization Name",
            "description" => "Description for your pass",
            "backgroundColor" => "rgb(255, 255, 255)",
            "foregroundColor" => "rgb(0, 80, 136)",
            "storeCard" => [
                "secondaryFields" => [
                    [
                        "key" => "name",
                        "label" => "Name",
                        "value" => $name
                    ],
                    [
                        "key" => "accountNumber",
                        "label" => "Account Number",
                        "value" => $accountNumber
                    ],
                ],
            ],
        ];




    try {
 
            $pass = new PKPass('key_new.p12', 'password for p12 file');
            $pass->setData(json_encode($passJson));

            // Add files to the PKPass package
            $pass->addFile("{$passFolder}/icon.png");
            $pass->addFile("{$passFolder}/icon@2x.png");
            $pass->addFile("{$passFolder}/logo.png");
            $pass->addFile("{$passFolder}/strip.png");

            // Create the pass
            $pass->create(true);


           
            // Delete temp files and folder
            array_map('unlink', glob("{$passFolder}/*.*"));
            rmdir($passFolder);
        } catch (Exception $e) {
            echo 'Error: ' . $e->getMessage();
            exit;
        }
    }

   }

   else {

    echo "Invalid submission.";
 }










#######When you encounter an OPENSSL error with the Certificates.p12######

**** OPENSSL PROBLEM WITH APPLE CERTIFICATES
  download legacy.dll and put it in lib

Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Try the new cross-platform PowerShell https://aka.ms/pscore6


PS C:\Users\ipedeglorio> cd Certificates2 

PS C:\Users\ipedeglorio\Certificates2> $env:OPENSSL_MODULES="C:\Users\ipedeglorio\OpenSSL-Win64\lib\ossl-modules"

PS C:\Users\ipedeglorio\Certificates2> openssl pkcs12 -legacy -in Certificates.p12 -nodes -out key_decrypted.tmp

Enter Import Password:

PS C:\Users\ipedeglorio\Certificates2> openssl pkcs12 -in key_decrypted.tmp -export -out key_new.p12

Enter Export Password:

Verifying - Enter Export Password:

##### Here are my logs when I did it#########

   95  DMSs-iMac:Certificates2 dmsstudio$ ls -l Certificates.p12
   
   96  ls -l Certificates.p12
   
   97  openssl pkcs12 -in Certificates.p12 -nodes -out key_decrypted.tmp
   
   98  openssl pkcs12 -in key_decrypted.tmp -export -out key_new.p12
   
   99  openssl pkcs12 -legacy -in Certificates.p12 -nodes -out key_decrypted.tmp


  100  openssl pkcs12 -in key_decrypted.tmp -export -out key_new.p12
  
  101  openssl pkcs12 -legacy -in Certificates.p12 -nodes -out key_decrypted.tmp
  
  102  openssl pkcs12 -legacy -in Certificates.p12 -nodes -out key_decrypted.tmp
  
  103  openssl pkcs12 -legacy -in Certificates.p12 -nodes -out key_decrypted.tmp
  
  104  openssl pkcs12 -legacy -in Certificates.p12 -nodes -out key_decrypted.tmp
  
  105  cd Desktop
  
  106  cd certificates.p12
  
  107  cd Certificates.p12

  108  cd Certificates2
  
  109  openssl pkcs12 -legacy -in Certificates.p12 -nodes -out key_decrypted.tmp
  
  110  openssl pkcs12 -in key_decrypted.tmp -export -out key_new.p12
  
  111  cd etc
  
  112  cd usr
  
  113  which openssl
  
  114  sudo find / -name openssl.cnf 2>/dev/null
  
  115  sudo nano /private/etc/ssl/openssl.cnf
  
  116  cd Desktop
  
  117  cd §Certificates2
  
  118  cd Certificates2
  
  119  openssl pkcs12 -legacy -in Certificates.p12 -nodes -out key_decrypted.tmp
  
  120  openssl pkcs12 -in key_decrypted.tmp -export -out key_new.p12
  
  121  openssl pkcs12 -legacy -in Certificates.p12 -nodes -out key_decrypted.tmp
  
  122  openssl pkcs12 -in key_decrypted.tmp -export -out key_new.p12
  
  123  history 29








Useful Links
https://developer.apple.com/help/account/create-certificates/create-a-certificate-signing-request/
