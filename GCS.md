# Ground Control Software

## Architecture

The driver for the telemetry radio is in `communication/radio.py`. When the driver receives a new packet, it emits a `tlm_recv` signal along with the latest packet and radio information. 

The telemetry model in `app/models/telemetry_model.py` listens to the radio. When a signal is received, it emits a `data_changed` signal along with the latest packet and radio information. Each view is managed by its controller. The controller listens to the `data_changed` signal and updates the view using the new latest packet. The controller listens to the view and if there is a user input, the controller updates the config model in `app/models/config_model.py`. The config model then emits a signal which informs and updates the other views with the user input.

The config model is a mediator in the Mediator Design Pattern. When views need information from another, they tell config model. The config model figures out how to get the needed information from the other views. The config model thne relays the information back to the requesting view. Views communicate indirectly through the config model, keeping things organized.

## Resources

https://ieeexplore.ieee.org/document/4488945

https://ieeexplore.ieee.org/document/5598080
