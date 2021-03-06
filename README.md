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

Institution's network domain must be .ac.uk: https://accounts.jasmin.ac.uk/services/reverse_dns_check/

d) Request ssh login access to JASMIN

e) Apply for GWS access (e.g. cmip6-prep and pmip4)

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

    d) Cache password
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
    
    e) Access the MOSRS repository
    rosie go
    [MOSRS password]
    [pop-up window]
    
    f) rosie GUI
    
    Follow instructions: http://cms.ncas.ac.uk/wiki/CDDS/halo

# 4. Conversion process

a) Under /group_workspaces, create a top-level directory for the experiment - this will later become synonymous with $CDDS_DIR (mine is users/racheley/cmor_test/).

b) cd to the experiment directory - all work should take place here

c) Copy the conversion process orchestration script cdds_workflow_for_users.sh from central-location to this directory:

    cdds v1.3.2: wget https://raw.githubusercontent.com/NCAS-CMS/NCAS-Useful-Documentation/master/cdds_operationl_scripts/cdds_workflow_for_user_v132.sh

    cdds v1.3.3: wget https://raw.githubusercontent.com/NCAS-CMS/NCAS-Useful-Documentation/master/cdds_operationl_scripts/cdds_workflow_for_user_v133.sh

d) Rename the script

    mv cdds_workflow_for_user_v132.sh cdds_workflow_for_user.sh

e) Edit the script to set the environment variables CDDS_DIR, REQUEST_JSON and FILEPATHSNAME

    export CDDS_DIR = "/gws/nopw/j04/pmip4_vol2/users/racheley/cmor_test"
    REQUEST_JSON = ba469_json.json
    export FILEPATHSTYPE = "ARCHER"

f) Create the json request file, with the same name as REQUEST_JSON

The CDDS workflow is driven by the json request file, which holds info about MIP, experiment, streams to be processed, start & end dates, source model suite id etc. 

g) Begin the CDDS process:

    source cdds_workflow_for_user.sh

[error]

    No JSON object could be decoded
    Traceback (most recent call last):
      File "/gws/smf/j04/cmip6_prep/cdds-env/r7785_trunk_cdds_v132/cdds_prepare/cdds_prepare/command_line.py", line 46, in main_create_cdds_directory_structure
        create_cdds_directory_structure(args)
      File "/gws/smf/j04/cmip6_prep/cdds-env/r7785_trunk_cdds_v132/cdds_prepare/cdds_prepare/directory_structure.py", line 26, in create_cdds_directory_structure
        request = read_request(arguments.request, REQUIRED_KEYS_FOR_PROC_DIRECTORY)
      File "/gws/smf/j04/cmip6_prep/cdds-env/r7785_trunk_cdds_v132/hadsdk/hadsdk/request.py", line 43, in read_request
        items = read_json(request_path)
      File "/gws/smf/j04/cmip6_prep/cdds-env/r7785_trunk_cdds_v132/hadsdk/hadsdk/common.py", line 188, in read_json
        data = json.load(file_handle)
      File "/gws/smf/j04/cmip6_prep/jaspy_base/jaspy/miniconda_envs/jaspy2.7/m2-4.6.14/envs/cdds-env-r20200204/lib/python2.7/json/__init__.py", line 291, in load
        **kw)
      File "/gws/smf/j04/cmip6_prep/jaspy_base/jaspy/miniconda_envs/jaspy2.7/m2-4.6.14/envs/cdds-env-r20200204/lib/python2.7/json/__init__.py", line 339, in loads
        return _default_decoder.decode(s)
      File "/gws/smf/j04/cmip6_prep/jaspy_base/jaspy/miniconda_envs/jaspy2.7/m2-4.6.14/envs/cdds-env-r20200204/lib/python2.7/json/decoder.py", line 364, in decode
        obj, end = self.raw_decode(s, idx=_w(s, 0).end())
      File "/gws/smf/j04/cmip6_prep/jaspy_base/jaspy/miniconda_envs/jaspy2.7/m2-4.6.14/envs/cdds-env-r20200204/lib/python2.7/json/decoder.py", line 382, in raw_decode
        raise ValueError("No JSON object could be decoded")
    ValueError: No JSON object could be decoded
    /gws/nopw/j04/pmip4_vol2/users/racheley/cmor_test//create_cdds_directory_structure_2020-04-08T1317Z.log:Traceback (most recent call last):
    /gws/nopw/j04/pmip4_vol2/users/racheley/cmor_test//create_cdds_directory_structure_2020-04-08T1355Z.log:Traceback (most recent call last):
    /gws/nopw/j04/pmip4_vol2/users/racheley/cmor_test//create_cdds_directory_structure_2020-04-08T1507Z.log:Traceback (most recent call last):
    Found Python Traceback in /gws/nopw/j04/pmip4_vol2/users/racheley/cmor_test//*create_cdds_directory_structure*.log*. Stopping.
    
    
    
    
    
