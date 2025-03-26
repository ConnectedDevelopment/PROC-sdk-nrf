################################
nRF SDK Repository for Excalibur
################################

This repository was created to fix an issue found in the `nrk-sdk <https://github.com/nrfconnect/sdk-nrf>`_.

The issue caused a compilation error when building a non-secure application for the BLE processor (nRF54L15)
that included support for NFC tags.  This issue was brought to Nordic's attention, but there were no immediate
plans to fix the issue.

The file with the issue was the ``subsys/nfc/lib/platform.c``, located in the ``nfc_platform_tagheaders_get()``
routine.  The change allows the non-secure build to complete without error, but the correct operation of the
modified routine was not verified, as it is not used in the hardware verification application.

Device Errata
#############

Nordic published an errata 

  nRF Connect SDK – errata handling 
  All customers must migrate to the latest nRF Connect SDK release v2.9.1 before moving their product to
  production. This release introduces handling for critical errata (errata 39). 
  Please see the release notes for more details.
  `nRF Connect SDK v2.9.1 Release Notes <https://docs.nordicsemi.com/bundle/ncs-2.9.1/page/nrf/releases_and_maturity/releases/release-notes-2.9.1.html?utm_campaign=Product+Update+Notifications+-+PUN&utm_medium=email&_hsenc=p2ANqtz-_HYF68AMatU8JuIkCkTgOuDwl5JoHf-INYYAZTqDj_MmYTLXEnyUTrVoUZW1a9Jec7RZIGyOr6VF2FsKz-JJv3CXb5Xw&_hsmi=353271918&utm_content=353271918&utm_source=hs_email>`_.

The errata is related to the Multiprotocol Service Layer (MPSL) and the v2.9.1 release provides a workaround
for the errata.

At this time (28-Mar-25), the main branch of the Nordic sdk-nrf does not include the v2.9.1 changes, so the main
branch of this repo was started from the v2.9.1 tag of the Nordic sdk-nrf repo.

Updating the repo
#################

In order to preserve the tags associated with the Nordic sdk-nrf repository, it is necessary to use
``git rebase`` instead of ``git merge`` or ``git pull`` when updating this repository with changes from the
Nordic repository.  Without having the tags preserved, it becomes difficult to determine which version of the
SDK is being used.

If the issue with the compilation failing for the non-secure build is fixed in the Nordic SDK, the references
to this repository can be replaced with the Nordic repository.

Update steps
------------

The Nordic sdk-nrf repository should be included as a remote called ``upstream``.

``git remote add upstream https://github.com/nrfconnect/sdk-nrf``

In this repository, make sure to start on the ``main`` branch.

``git checkout main``

Fetch any changes from the Nordic repository.

``git fetch upstream``

Rebase the changes into the repository.

``git rebase upstream/main``

Push the changes back to the repository

``git push``
