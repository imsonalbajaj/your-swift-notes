

## Deeplink
Deep links open the app only if it is installed; otherwise the system cannot resolve the URL and nothing happens.  
- Opens the app only if installed
- Uses custom schemes like whatsapp://
- No web fallback
- No security validation by iOS

```
//ex - this func open whatsapp from your app
func openWhatsApp(msgStr: String, for num :String){
        
    let urlWhats = "whatsapp://send?phone=\(num)&text=\(msgStr)"
    if let urlString = urlWhats.addingPercentEncoding(withAllowedCharacters: CharacterSet.urlQueryAllowed) {
        if let whatsappURL = URL(string: urlString) {
            if UIApplication.shared.canOpenURL(whatsappURL) {
                UIApplication.shared.open(whatsappURL, options: [:], completionHandler: nil)
                
            }
        }
    }
    
}
```

## Universal Link

Universal Links use standard HTTPS URLs that open the app when installed and fall back to the website when the app is not installed.


### How Universal Links works?
1. When the app is installed, iOS reads the app’s Associated Domains entitlement.
2. For each associated domain, iOS attempts to download the apple-app-site-association (AASA) file from:
```
https://domain/.well-known/apple-app-site-association
```

or fallback:

```
https://domain/apple-app-site-association
```

3. iOS parses the AASA file and checks the Team ID + Bundle ID combination against the appID entries under:  
applinks → details → appID

4. If a match is found, iOS associates the declared paths for that domain with the app and caches the AASA file.

5. When the user taps an HTTPS link:  
    - iOS checks whether the domain has a cached AASA
    - Then checks whether the URL path matches one of the allowed paths
    - If matched → opens the app
    - If not matched or AASA missing → opens Safari


