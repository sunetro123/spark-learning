# Working on a 'Moving' Branch

git checkout moving_branch """Moving branch of that day"""

git checkout -b clone_moving_branch "Clone of the same HEAD"

``` Commit and Push on *Moving* Branch ( Not the Master) ```
Now How to make *clone_moving_branch* same as "changed" moving branch 

>> git checkout clone_moving_branch

"""
:And here is the magic 
"""
git merge *moving_branch*

Next --> How to commit clone_moving_branch to moving branch

