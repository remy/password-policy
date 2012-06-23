As per https://github.com/remy/password-policy

This is the starting point for the password-policy.info web site and index page.  I (@rem) thinks it makes sense to apply some rules - things like preventing three of the same character kinda makes sense, but maybe it depends on your application?  

Provide easy to understand guide to choosing strong passwords for **end users**, that we can ALL link to, translate this into as many international languages as possible, license it, making it EASY for anyone to re-use it. And provide one hit language specific libraries that implement these rules in a simple function call.

# Rules

- Allow spaces (and don't trim them), better yet, don't disallow a certain sensible range
- Is there anything that should not be allowed??? (rem - I don't think so) (maybe block rtl override (U+202E) and null (U+0000))
- No limit on length (or maybe there should be based on typical hashed lengths - or does this matter?) hashes have a pre-determined length that does, afaik, not depend on the input, so no length limit
- character encoding?
- Don't enforce requirements for numbers of special characters - 'mary had a little lamb it's fleece was white as snow' is a perfectly valid password
- How do you ensure it's longer than X characters and secure - should you prevent "aaaaaaaa" (as per the rule in the intro - that's an Apple one)
- In the end it's people's own responsibility so don't enforce too much, tips for a strong password are very welcome, but if someone thinks 'aaaa' is safe enough…
- Encourage users to choose strong passwords; see http://www.baekdal.com/insights/password-security-usability (problem is: people don't read - it's sad, but true - they need to be pushed in the rigth direction)
- Twitter have a list of 'bad passwords' you should draw on these. (good idea - a dictionary of blocked passwords) I think they're in their JS libs somewhere (Dictionary's can be pretty big and a JS solution might only want to concentrate on the top 10-50 bad passwords.)
- Entropy http://xkcd.com/936/
- JS password security checker: http://howsecureismypshould you prevent "aaaaaaaa" (as per the rassword.net/ - may be good inspiration

# Server Side implementation guideline

(@rem has added this, but really doesn't know what he's talking about)

- Never store original password(x100)
- Never email user their original password - always reset (how?)(should be impossible if you don't know it…)Some sites email your password after creation and before they throw it away after encryption (assuming they encrypt it) - so, no, don't do that.
- Instead of resetting a PW, mail the user a one-time token (with limited lifespan, say 1h or 24h) that (maybe take them to reset password over HTTPS? <-- good point)logs them in again. But keep their old PW valid. User can change the PW then, when logged in with the one-time token. (Advantage: if user reminds the PW again, she doesn't have to change it at all.) (You'd still need to do some additional verification of the user even w/a token so that hijacked e-mail can't reset the password. User would need to hit a URL, provide some additional verification information, before being let in the system. Maybe even suggest using a 2-step verification system for password resets like Google--http://support.google.com/a/bin/answer.py?hl=en&answer=175197) - Possibly use something like Twilio to send additional auth via SMS?
- Access to password settings page MUST be SSL-secured (even if the rest of your site isn't -- which is suboptimal, but okay for some scenarios) (if a hacker can access non-SSL stuff and get your session ID, what's stopping them accessing the SSL bit? surely SSL is all or none)
- Salts different per user/account - unique(x100) Salts? lolwut? You should be using something like bcrypt, or you may as well store in plaintext (http://codahale.com/how-to-safely-store-a-password/) PCI compliance (credit card stuff) allows for sha256 hash, with a two random character.
- Sensible Two-way algorithms ("encryption functions") (if 2-way algorithms are being listed then it should be noted that 1-way is better; maybe this should be in an "advanced" section):
  - There are none. Don’t be stupid and insecure, just use a one-way algo.
- Sensible One-way algorithms ("hash functions"):
  - bcrypt (avaiable in all languages? If not, what is the alternative?)
  - PBKDF2 https://en.wikipedia.org/wiki/PBKDF2
  - scrypt
  - SHA512 (strong enough on today's HW??) - e.g. EC2 attack (SHA512 isn't designed for storing passwords long-term, it should be avoided)
- Never store tokens in cookies that can log people in without checking validity of their account (store one-time values in cookies that are checked and replaced on use)
 - Here is a Mozilla blog post about password hash: http://blog.mozilla.org/webdev/2012/06/08/lets-talk-about-password-storage/ Good reference.
- If this is PHP (or any other popular language), why are rolling your own password algorithm? When you can have a framework/library can handle it for you? such as Symfony2?

# (Optional) Usability features

- Save multiple versions of PW (hashed, as described above, of course) to provide better usability
  See http://www.hackhow.com/log-facebook-account-using-3-passwords/ how Facebook helps especially mobile users to enter their password "correctly".
  - Original: "passWord"
  - First uppercase (as mobile keyboards sometimes do it): "PassWord"
  - Caps inverted: "PASSwORD"
    - simplified above idea make it case insensitive? Or indicated that it's case sensitive, and don't implement.
  - issue, requires https on first go, and password is still transferred plain text.
- Google 2-step verification http://support.google.com/a/bin/answer.py?hl=en&answer=175197

# Languages

Please volunteer yourself

- JavaScript - @rem
- PHP (already has https://github.com/ircmaxell/PHP-PasswordLib - what else should a library cover?, ping @dave1010 if no one else is interested in the PHP one)
  - PHP framework, symfony 2.  http://symfony.com allows for ACL as well.  Think group collaboration. - @alpha1125
- Ruby
- Python
- .NET (C#)

