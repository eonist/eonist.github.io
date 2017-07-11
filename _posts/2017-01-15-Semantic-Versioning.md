My notes on Semantic Versioning <!--more--> 

## Semantic Versioning
The three numbers are defined as major, minor, and patch version numbers. For example, the version number 0.9.0 would be interpreted as:

- **Major**: When the major number is increased, this means that non-backwards compatible changes were introduced. When you upgrade a pod to the next major version, you may need to fix build errors, or the pod may behave differently than before.

- **Minor:** When the minor number is increased, this means new functionality was added, but it’s backwards compatible. When you decide to upgrade, you may or may not need the new functionality, but it shouldn’t cause any build errors or change existing behavior.

- **Patch:** When the patch number is increased, this means bug fixes were added, but no new functionality was added or behavior changes made. In general, you always want to upgrade patch versions as soon as possible to have the latest, stable version of the pod.
Lastly, the highest order number (major then minor then patch) must be increased per the above rules and any lower order numbers must be reset to zero.
Need an Example?
Consider a pod that has a current version number of 1.2.3.
If changes are made that are not backwards compatible, don’t have new functionality, but fix existing bugs, the next version would be 2.0.0.  

⚠️️If a version number is less than 1.0.0, it’s considered to be a beta version, and minor number increases may include backwards incompatible changes.⚠️️