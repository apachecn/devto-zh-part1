# curl -sSL https://get.rvm.io

> 原文：<https://dev.to/psnebc/curl--ssl-httpsgetrvmio-2jc>

```
patrick@lp:~ > \curl -sSL https://get.rvm.io | bash -s stable --ruby
Downloading https://github.com/rvm/rvm/archive/1.26.11.tar.gz
Downloading https://github.com/rvm/rvm/releases/download/1.26.11/1.26.11.tar.gz.asc
gpg: Signatur vom Mon 30 MÃ¤r 2015 23:52:13 CEST mittels RSA-SchlÃ¼ssel ID BF04FF17
gpg: Korrekte Signatur von "Michal Papis (RVM signing) <mpapis@gmail.com>"
gpg: WARNUNG: Dieser SchlÃ¼ssel trÃ¤gt keine vertrauenswÃ¼rdige Signatur!
gpg:          Es gibt keinen Hinweis, daÃŸ die Signatur wirklich dem vorgeblichen Besitzer gehÃ¶rt.
Haupt-Fingerabdruck  = 409B 6B17 96C2 7546 2A17  0311 3804 BB82 D39D C0E3
     Unter-Fingerabdruck  = 62C9 E5F4 DA30 0D94 AC36  166B E206 C29F BF04 FF17
GPG verified '/home/patrick/.rvm/archives/rvm-1.26.11.tgz'

Upgrading the RVM installation in /home/patrick/.rvm/
    RVM PATH line found in /home/patrick/.mkshrc /home/patrick/.profile /home/patrick/.bashrc /home/patrick/.zshrc.
    RVM sourcing line found in /home/patrick/.profile /home/patrick/.bash_profile /home/patrick/.zlogin.
    Fixing environment for ruby-2.2.1.
ruby-2.2.1 - #gemset created /home/patrick/.rvm/gems/ruby-2.2.1
ruby-2.2.1 - #importing gemsetfile /home/patrick/.rvm/gemsets/default.gems evaluated to empty gem list
ruby-2.2.1 - #generating default wrappers..............
Upgrade of RVM in /home/patrick/.rvm/ is complete.

# patrick,
# 
#   Thank you for using RVM! 
#   We sincerely hope that RVM helps to make your life easier and more enjoyable!!! 
# 
# ~Wayne, Michal & team. 

In case of problems: http://rvm.io/help and https://twitter.com/rvm_io                                                                                                                        

Upgrade Notes:                                                                                                                                                                                

  * No new notes to display.                                                                                                                                                                  

rvm 1.26.11 (latest) by Wayne E. Seguin <wayneeseguin@gmail.com>, Michal Papis <mpapis@gmail.com> [https://rvm.io/]                                                                           
Already installed ruby-2.2.1\.                                                                                                                                                                 
To reinstall use:                                                                                                                                                                             

    rvm reinstall ruby-2.2.1                                                                                                                                                                  

Creating alias default for ruby-2.2.1...                                                                                                                                                      

  * To start using RVM you need to run `source /home/patrick/.rvm/scripts/rvm`                                                                                                                
    in all your open shell windows, in rare cases you need to reopen all shell windows. 
```

Enter fullscreen mode Exit fullscreen mode