# centreon-plugins-datas

## Method to update centreon-plugins-datas folder in case of new centreon-plugins 


````
#in my case, i have chosen to download the github repositories in github user home folder.
$cd /home/github/
#download centreon-plugins repository
$git clone git@github.com:centreon/centreon-plugins.git
#download centreon-plugins-datas repository
$git clone git@github.com:alexvea/centreon-plugins-datas.git
#check if new folder has been created in centreon-plugins, if so, it will create the folder in centreon-plugins-data with a .gitkeep file. We ignore mode, custom mode, components folders which we don't need.
$for folder in `find /home/github/centreon-plugins/src -type d | grep -v "mode$" | grep -v "components$" | grep -v "custom$"`; do PATH_TO_CHECK="centreon-plugins-datas/${folder/\/home\/github\/centreon-plugins\//}"; [[ ! -d ${PATH_TO_CHECK} ]]  && mkdir -v -p ${PATH_TO_CHECK} && touch ${PATH_TO_CHECK}/.gitkeep && echo "touch: .gitkeep for ${PATH_TO_CHECK} folder"; done
#check if need to commit and PR
$cd /home/github/centreon-plugins-datas
$git status
# On branch main
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#       src/storage/wd/nas/snmp/tata/
nothing added to commit but untracked files present (use "git add" to track)
#commit the new plugin folder
$git commit -m "create folder for XXX"
#pull request on main branch
$git push origin main
````
