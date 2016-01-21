## Submodules
After cloning repository, make sure you clone also its submodules:

```shell
git submodule init
git submodule update
```

## How to get started

- [Download](https://github.com/BlinkID/blinkid-phonegap/archive/master.zip) BlinkID PhoneGap SDK, and try the sample app for iOS od Android.

Sample app is generated with a script

```shell
./initDemoApp.sh
```

To run iOS demo application open Xcode project BlinkIdDemo.xcodeproj

To run Android demo application type

```shell
cordova run android
```

- [Generate](https://microblink.com/login?url=/customer/generatedemolicence) a **free demo license key** to start using the SDK in your app (registration required)

- Get information about pricing and licensing of [BlinkID](https://microblink.com/blinkid)

## Installation

First generate a empty project if needed:

```shell
cordova create <path> <package> <name>
```

> The shown instructions are for **Cordova**, the instructions for **PhoneGap** are practically the same, except for some slight command line argument differences.

Add the **BlinkID** plugin to your project:

```shell
cd <path_to_your_project>
cordova plugin add <blinkID_plugin_path>
```

### Android

Add Android platform support to the project:

    cordova platform add android
    
### iOS

Add iOS plaform support to the project:

    cordova platform add ios

## Sample

Here's a complete example of how to create and build a project for **Android** and **iOS** using **cordova** (you can substitute equivalent commands for **phonegap**):

```shell
# pull the plugin and sample application from Github
git clone git@github.com:BlinkID/blinkid-phonegap.git

# initialize and update submodules
git submodule init
git submodule update

# create a empty application
cordova create testcordova

cd testcordova

# add the blinkID plugin
cordova plugin add ../blinkid-phonegap/BlinkID

# add android support to the project
cordova platform add android

# build the project, the binary will appear in the bin/ folder
cordova build android

# add ios support to the project
cordova platform add ios

# build the project
cordova build ios
```

In **phonegap** CLI instead of "platform add" just request a build for the platform using "build android" or "build ios". You will have to do the manual steps described above to be able to do a successfull build.

You can also use provided `initDemoApp.sh` script that will generate a demo app that uses the plugin:

```shell
./initDemoApp.sh
```

To run the script, you'll need BASH environment on Windows (Linux and MacOS use BASH by default).


## Usage

To use the plugin you call it in your Javascript code like the demo application:

```javascript

/**
* Use these scanner types
* Available: "PDF417", "USDL", "Bar Decoder", "Zxing", "MRTD", "UKDL", "MyKad"
* PDF417 - scans PDF417 barcodes
* USDL - scans barcodes located on the back of US driver's license
* Bar Decoder - scans code39 and code128 type barcodes. Both Code 39 and Code 128 are scanned by default when using Bar Decoder.
* Zxing - scans various types of codes (i.e. QR, Aztec). Types of scanned codes can be modified in plugin classes (Explained later in this readme). By default, scanned codes are set to: Code 39, Code 128, EAN 13, EAN 8, QR, UPCA, UPCE
* MRTD - scans Machine Readable Travel Document, contained in various IDs and passports
* UKDL - scans the front of United Kingom driver's license
* MyKad - scans the front of Malaysian ID cards
* Variable << types >> declared below has to contain all the scanners needed by your application. Applying additional scanners will slow down the scanning process
*/
var types = ["PDF417", "UKDL", "MRTD"];

// Note that each platform requires its own license key

// This license key allows setting overlay views for this application ID: com.microblink.blinkid
var licenseiOs = "OLJJAUDF-CIV2HMG3-ZFEVNWIC-2FNSXP3W-YLKHF4MV-LTSI5GR7-I5ARBPXV-WRCTMCMT";

// This license is only valid for package name "com.microblink.blinkid"
var licenseAndroid = "NFRZVYWD-MCK7SSO7-TJ7ZWOC4-AT2AYDM7-JDHZQMHY-V3PZU4SX-54PGUFQM-AUX5RGYJ";

scanButton.addEventListener('click', function() {    
    cordova.plugins.blinkIdScanner.scan(
            
        // Register the callback handler
        function callback(scanningResult) {
                    
            // handle cancelled scanning
            if (scanningResult.cancelled == true) {
                resultDiv.innerHTML = "Cancelled!";
                return;
            }
                    
            // Obtain list of recognizer results
            var resultList = scanningResult.resultList;
                    
            // Iterate through all results
            for (var i = 0; i < resultList.length; i++) {

                // Get individual resilt
                var recognizerResult = resultList[i];
                if (recognizerResult.resultType == "Barcode result") {
                    // handle Barcode scanning result
    
                    var raw = "";
                    if (typeof(recognizerResult.raw) != "undefined" && recognizerResult.raw != null) {
                        raw = " (raw: " + hex2a(recognizerResult.raw) + ")";
                    }
                    resultDiv.innerHTML = "Data: " + recognizerResult.data + raw +
                                        " (Type: " + recognizerResult.type + ")";
    
                } else if (recognizerResult.resultType == "USDL result") {
                    // handle USDL parsing result
    
                    var fields = recognizerResult.fields;
    
                    resultDiv.innerHTML = /** Personal information */
                                           "USDL version: " + fields[kPPStandardVersionNumber] + "; " +
                                           "Family name: " + fields[kPPCustomerFamilyName] + "; " +
                                           "First name: " + fields[kPPCustomerFirstName] + "; " +
                                           "Date of birth: " + fields[kPPDateOfBirth] + "; " +
                                           "Sex: " + fields[kPPSex] + "; " +
                                           "Eye color: " + fields[kPPEyeColor] + "; " +
                                           "Height: " + fields[kPPHeight] + "; " +
                                           "Street: " + fields[kPPAddressStreet] + "; " +
                                           "City: " + fields[kPPAddressCity] + "; " +
                                           "Jurisdiction: " + fields[kPPAddressJurisdictionCode] + "; " +
                                           "Postal code: " + fields[kPPAddressPostalCode] + "; " +
    
                                            /** License information */
                                            "Issue date: " + fields[kPPDocumentIssueDate] + "; " +
                                            "Expiration date: " + fields[kPPDocumentExpirationDate] + "; " +
                                            "Issuer ID: " + fields[kPPIssuerIdentificationNumber] + "; " +
                                            "Jurisdiction version: " + fields[kPPJurisdictionVersionNumber] + "; " +
                                            "Vehicle class: " + fields[kPPJurisdictionVehicleClass] + "; " +
                                            "Restrictions: " + fields[kPPJurisdictionRestrictionCodes] + "; " +
                                            "Endorsments: " + fields[kPPJurisdictionEndorsementCodes] + "; " +
                                            "Customer ID: " + fields[kPPCustomerIdNumber] + "; ";

                } else if (recognizerResult.resultType == "MRTD result") {
                            
                    var fields = recognizerResult.fields;
    
                    resultDiv.innerHTML = /** Personal information */
                                            "Family name: " + fields[kPPmrtdPrimaryId] + "; " +
                                            "First name: " + fields[kPPmrtdSecondaryId] + "; " +
                                            "Date of birth: " + fields[kPPmrtdBirthDate] + "; " +
                                            "Sex: " + fields[kPPmrtdSex] + "; " +
                                            "Nationality: " + fields[kPPmrtdNationality] + "; " +
                                            "Date of Expiry: " + fields[kPPmrtdExpiry] + "; " +
                                            "Document Code: " + fields[kPPmrtdDocCode] + "; " +
                                            "Document Number: " + fields[kPPmrtdDocNumber] + "; " +
                                            "Issuer: " + fields[kPPmrtdIssuer] + "; " +
                                            "ID Type: " + fields[kPPmrtdDataType] + "; " +
                                            "Opt1: " + fields[kPPmrtdOpt1] + "; " +
                                            "Opt2: " + fields[kPPmrtdOpt2] + "; ";
    
                } else if (recognizerResult.resultType == "UKDL result") {
                                
                    var fields = recognizerResult.fields;
    
                    resultDiv.innerHTML = /** Personal information */
                                            "ID Type: " + fields[kPPukdlDataType] + "; " +
                                            "Date of Expiry: : " + fields[kPPukdlExpiry] + "; " +
                                            "Issue Date: " + fields[kPPukdlIssueDate] + "; " +
                                            "Driver Number: " + fields[kPPukdlDriverNumber] + "; " +
                                            "Address: " + fields[kPPukdlAddress] + "; " +
                                            "Birth Data: " + fields[kPPukdlBirthData] + "; " +
                                            "First name: " + fields[kPPukdlFirstName] + "; " +
                                            "Last name: " + fields[kPPukdlLastName] + "; ";
    
                } else if (recognizerResult.resultType == "MyKad result") {
                                
                    var fields = recognizerResult.fields;
    
                    resultDiv.innerHTML = /** Personal information */
                                            "ID Type: " + fields[kPPmyKadDataType] + "; " +
                                            "NRIC Number: " + fields[kPPmyKadNricNumber] + "; " +
                                            "Address: " + fields[kPPmyKadAddress] + "; " +
                                            "Birth Date: " + fields[kPPmyKadBirthDate] + "; " +
                                            "Full Name: " + fields[kPPmyKadFullName] + "; " +
                                            "Religion: " + fields[kPPmyKadReligion] + "; " +
                                            "Sex: " + fields[kPPmyKadSex] + "; ";
                }
            }   
        },
                
        // Register the error callback
        function errorHandler(err) {
            alert('Error: ' + err);
        },

        types, licenseiOs, licenseAndroid
    );
});
```
+ Available scanners are:
    + **PDF417** - scans PDF417 barcodes
    + **USDL**  - scans barcodes located on the back of US driver's license
    + **Bar Decoder** - scans code39 and code128 type barcodes. Both Code 39 and Code 128 are scanned by default when using Bar Decoder.
    + **Zxing** - scans various types of codes (i.e. QR, Aztec). Types of scanned codes can be modified in plugin classes (Explained later in this readme). By default, scanned codes are set to: Code 39, Code 128, EAN 13, EAN 8, QR, UPCA, UPCE
    + **MRTD** - scans Machine Readable Travel Document, contained in various IDs and passports
    + **UKDL** - scans the front of United Kingom driver's license
    + **MyKad** - scans the front of Malaysian ID cards


+ All license parameters must be provided (for **iOS** and **Android**) even if you do not plan to run the application on both platforms. The licenses that you do not have/use must be set to `null`.


## Changing scanner settings

To change scanner settings you need to modify Phonegap plugin classes for iOS and Android. Plugin classes are located in ./BlinkID/src . All necessary settings documentation is located in those source files.
