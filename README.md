# Metainstruments_multidevice
## With this class you can read data points from multiple devices and plot them against each others

# here is the class:
class conductance_from_agilent(qc.MultiParameter):
    def __init__(self, IV_gain, agilent_handle , scale_param , dc_source ):
        super().__init__('agilent_current', names=('R_2t' , 'G_2t' , 'I_2t', 'X_2t'), shapes=((), (), (), ()),
                         labels=('Resistance 2t', 'Conductance 2t', 'Current DC', 'Raw agilent voltage'),
                         units=('Ohm','e^2/h', 'A', 'V'),
                         setpoints=((), (), (), ()),
                         docstring='Resistance and conductance (DC), using measured DC current and applied voltage bias')
        #Multimeter parameters
        self._IV_gain = IV_gain
        self._agilent_handle = agilent_handle
        #DC voltage source parameters, Infact, this is the setpoints of the powersupply.
        self._scale_param = float(scale_param)
        self._dc_source = dc_source        
    
    def get_raw(self):
        # Lets read the data points from the multimeter
        voltage = self._agilent_handle.volt.get()
        agilent_current = voltage/self._IV_gain
        # Lets read the setpoints of the DC power supply
        raw_voltage = self._dc_source.get()
        real_voltage = raw_voltage * self._scale_param  
        
        const_e = 1.60217662e-19
        const_h = 6.62607004e-34        
        
        G_2t = agilent_current / real_voltage /const_e**2 * const_h
        R_2t = real_voltage / agilent_current
       
        return (R_2t , G_2t, agilent_current, voltage , real_voltage)
        
#Here you can set the parameters, there for place it in a separate cell
DC_Resistance = conductance_cur_from_agilent(IV_gain=1e6,agilent_handle=agilent1 , scale_param = 1e-3 , dc_source = yoko.voltage)
