# What is the soft feature freeze?

The soft feature freeze is the beginning of the stabilization phase of edk2's
development process. By the date of the soft feature freeze,  developers must
have sent their patches to the mailing list **and** received positive
maintainer reviews (`Reviewed-by` or `Acked-by` tags). This means that
features, and in particular non-trivial ones, must have been accepted by
maintainers before the soft freeze date.

Between the soft feature freeze and the [hard feature
freeze](HardFeatureFreeze), previously reviewed and unit-tested features may be
applied (or merged) to the master branch, for integration testing. Feature
addition or enablement patches posted **or** reviewed after the soft feature
freeze will be delayed until after the upcoming [stable
tag](EDK-II#stable-tags).

# What should I do by the soft feature freeze?

As a maintainer, for any feature that you're targeting to the next [stable
tag](EDK-II#stable-tags), you should make sure that you've reviewed and
accepted the patches related to the feature.

As a developer, you probably should target a date that is at least 1-2 weeks
earlier than the soft freeze date. This will give the maintainer enough time to
review and test your patches. For major features you should probably
communicate with the maintainer about their intentions. It also helps if you:

- Enter a Bugzilla ticket for the [TianoCore Feature
  Requests](https://bugzilla.tianocore.org/enter_bug.cgi?product=Tianocore%20Feature%20Requests)
  product.

- Optionally, write a Feature page in the [edk2 wiki](Home) describing the
  feature and the motivation.

- On the [release planning wiki page](EDK-II-Release-Planning), link to your
  Bugzilla entry and/or the feature wiki page.

- Do all this early enough that you can work with the maintainer to get the
  review process underway.

(This definition is modeled after QEMU's [soft feature
freeze](https://wiki.qemu.org/Planning/SoftFeatureFreeze).)

# Announcing the soft feature freeze

The proposed schedule for the active development cycle is shown in the [EDK II
Release Planning](EDK-II-Release-Planning) article. Shortly before the soft
feature freeze, an announcement email is sent to the
[edk2-devel](https://lists.01.org/mailman/listinfo/edk2-devel) mailing list.
The announcement is posted by one of the maintainers that are listed in
[Maintainers.txt](https://github.com/tianocore/edk2/blob/master/Maintainers.txt),
in section `EDK II Releases`.
