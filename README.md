# CMORisation_process
This repositary describes how to undergo data CMOR'isation, according to the process at http://cms.ncas.ac.uk/wiki/CDDS.

# 1. MOSRS access
Request access to MOSRS
https://code.metoffice.gov.uk/trac/home

# 2. JASMIN Group Workspace (GWS) access
Follow the instructions at https://help.jasmin.ac.uk/article/189-get-started-with-jasmin. There's also a helpful webinar at https://www.youtube.com/watch?v=aHLMVW2bjdM&list=PLhF74YhqhjqlRhR2ACSC32eJ4FHzAstoz&index=2&t=0s 

a) Generate a SSH key

b) Get a JASMIN portal account
https://accounts.jasmin.ac.uk/account/profile/

c) Check network details

Institution's network domain must be .ac.uk
https://accounts.jasmin.ac.uk/services/reverse_dns_check/

d) Request ssh login access to JASMIN

e) Apply for GWS access

E.g. cmip6-prep and pmip4.

f) Login

    1. Start ssh-agent
    exec ssh-agent $SHELL 

    2. Add JASMIN private key
    ssh-add ~/.ssh/id_rsa_jasmin
    [jasmin passphrase]

    3.SSH to login server
    ssh -A -X <jasmin username>@jasmin-login1.ceda.ac.uk 

    4. SSH to jasmin-cylc server
    ssh -A -X jasmin-cylc.ceda.ac.uk 

# 3. Prepare the data
Several NEMO data sets need to be pre-processed to remove halos prior to processing. CMS have developed a Rose suite (u-bn582 - see http://cms.ncas.ac.uk/wiki/CDDS/halo) to automate halo removal - the suite is available from the MOSRS suite repository.

    a) If not already, log-in to jasmin-cylc 
    ssh -A -X jasmin-cylc.ceda.ac.uk 

    b) Add bash profile if necessary 
    cp /home/users/glister/.bash_profile /home/users/<username>/.bash_profile
    
    Same with bashrc
    cp /home/users/glister/.bashrc /home/users/<username>/.bashrc

    c) Configure your ~/.subversion/servers file
    https://code.metoffice.gov.uk/trac/home/wiki/FAQ

    Add this to the file:
    
      [groups]
      metofficesharedrepos = code*.metoffice.gov.uk

      [metofficesharedrepos]
      # Specify your Science Repository Service user name here
      username = <mosrs username>
      store-plaintext-passwords = no

    If ~/.subversion/servers does not exist, run svn once on the command line. 

    3. Cache password
    https://code.metoffice.gov.uk/trac/home/wiki/AuthenticationCaching 
    
    [log out and log in again]
    mosrs-cache-password
    
    *Possible error*
    gpg-agent: no process killed
    Error: gpg-agent not working
    
    Add this to the bottom of your .bashrc file:

    [[ $- != *i* ]] && return # Stop here if not running interactively
    #[[ $(hostname) = "jasmin-cylc.ceda.ac.uk" || $(hostname) = jasmin-sci*.ceda.ac.uk ]] && . mosrs-setup-gpg-agent
    [[ $(hostname) = "jasmin-cylc.ceda.ac.uk" ]] && . mosrs-setup-gpg-agent
    # Enable bash completion for Rose commands
    [[ -f /apps/contrib/metomi/rose/etc/rose-bash-completion ]] && . /apps/contrib/metomi/rose/etc/rose-bash-completion
    
    [log out and log in again]
    
    4. Access the MOSRS repository
    rosie go
    [MOSRS password]
    [pop-up window]
    
    5. rosie GUI
    
    Follow instructions: http://cms.ncas.ac.uk/wiki/CDDS/halo

# 4. Conversion process


    
    
    
    
    
    
