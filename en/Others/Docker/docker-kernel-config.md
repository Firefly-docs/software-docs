# Enable Needed Features

From the result you can find out which features are need to be enabled, back up the current .config: `cp .config .config.bk`, then use `make ARCH=arm64 menuconfig` to enter config menu, search and enable them. Please read the instructions in the config menu, if some features can't be selected, check the dependency.

Enabled all the necessary features and some optional features, remember to save. Compare the new .config and previous .config.bk to show the modifications you have made, then write them into the actual config file under `arch/arm64/configs/`

Then re-compile the kernel.
