Services
========

Each service is represented by a class that extend the ``BaseService`` class. They all implement 2 methods : ``make_request`` and ``interpret_response``.

   - ``make_request`` will return a ``Request`` instance ready to be sent to the ``Connection``
   - ``interpret_response`` will parse the ``data`` property inside a ``Response`` instance and populates another property named ``service_data``. This ``service_data`` property will be an instance of the ``ResponseData`` class that is nested within the service class.

**Crafting a request**

.. code-block:: python

   req = SomeService.make_request(param1, param2) 
   my_connection.send(req.get_payload())

**Parsing a response**

.. code-block:: python

   payload = my_connection.wait_frame(timeout=1)
   response = Response.from_payload(payload) 
   print('Raw data : %s' % response.data)
   SomeService.interpret_response(response, param1, param2) 
   print('Interpreted data : field1 : %s, field2 : %s' % (response.service_data.field1, response.service_data.field2))


.. _AccessTimingParameter:

AccessTimingParameter (0x83)
--------------------------------------

.. automethod:: udsoncan.services.AccessTimingParameter.make_request
.. automethod:: udsoncan.services.AccessTimingParameter.interpret_response

.. autoclass:: udsoncan.services::AccessTimingParameter.ResponseData
   :members: 

.. autoclass:: udsoncan.services::AccessTimingParameter.AccessType
   :members: 
   :undoc-members:
   :member-order: bysource

-------

.. _ClearDiagnosticInformation:

ClearDiagnosticInformation (0x14)
--------------------------------------

.. automethod:: udsoncan.services.ClearDiagnosticInformation.make_request
.. automethod:: udsoncan.services.ClearDiagnosticInformation.interpret_response

.. note:: This service have empty response data
.. note:: This service does not have subfunctions

-------

.. _CommunicationControl:

CommunicationControl (0x28)
--------------------------------------

.. automethod:: udsoncan.services.CommunicationControl.make_request
.. automethod:: udsoncan.services.CommunicationControl.interpret_response

.. autoclass:: udsoncan.services::CommunicationControl.ResponseData
   :members: 

.. autoclass:: udsoncan.services::CommunicationControl.ControlType
   :members: 
   :undoc-members:
   :member-order: bysource

-------

.. _ControlDTCSetting:

ControlDTCSetting (0x85)
--------------------------------------

.. automethod:: udsoncan.services.ControlDTCSetting.make_request
.. automethod:: udsoncan.services.ControlDTCSetting.interpret_response

.. autoclass:: udsoncan.services::ControlDTCSetting.ResponseData
   :members: 

.. autoclass:: udsoncan.services::ControlDTCSetting.SettingType
   :members: 
   :undoc-members:
   :exclude-members: vehicleManufacturerSpecific, systemSupplierSpecific
   :member-order: bysource

-------

.. _DiagnosticSessionControl:

DiagnosticSessionControl (0x10)
--------------------------------------

.. automethod:: udsoncan.services.DiagnosticSessionControl.make_request
.. automethod:: udsoncan.services.DiagnosticSessionControl.interpret_response

.. autoclass:: udsoncan.services::DiagnosticSessionControl.ResponseData
   :members: 

.. autoclass:: udsoncan.services::DiagnosticSessionControl.Session
   :members: 
   :undoc-members:
   :member-order: bysource

-------

.. _ECUReset:

ECUReset (0x11)
--------------------------------------

.. automethod:: udsoncan.services.ECUReset.make_request
.. automethod:: udsoncan.services.ECUReset.interpret_response

.. autoclass:: udsoncan.services::ECUReset.ResponseData
   :members: 

.. autoclass:: udsoncan.services::ECUReset.ResetType
   :members: 
   :undoc-members:
   :member-order: bysource

-------

.. _InputOutputControlByIdentifier:

InputOutputControlByIdentifier (0x2F)
--------------------------------------

.. automethod:: udsoncan.services.InputOutputControlByIdentifier.make_request
.. automethod:: udsoncan.services.InputOutputControlByIdentifier.interpret_response

.. autoclass:: udsoncan.services::InputOutputControlByIdentifier.ResponseData
   :members: 

.. note:: This service does not have subfunctions

**Example**

.. code-block:: python

   # Example taken from UDS standard

   class MyCompositeDidCodec(DidCodec):
      def encode(self, IAC_pintle, rpm, pedalA, pedalB, EGR_duty):
         pedal = (pedalA << 4) | pedalB
         return struct.pack('>BHBB', IAC_pintle, rpm, pedal, EGR_duty)

      def decode(self, payload):
         vals = struct.unpack('>BHBB', payload)
         return {
            'IAC_pintle': vals[0],
            'rpm'       : vals[1],
            'pedalA'    : (vals[2] >> 4) & 0xF,
            'pedalB'    : vals[2] & 0xF,
            'EGR_duty'  : vals[3]
         }

      def __len__(self):
         return 5    

   ioconfig = {
         0x132 : MyDidCodec,
         0x456 : '<HH',
         0x155 : {
            'codec' : MyCompositeDidCodec,
            'mask' : {
               'IAC_pintle': 0x80,
               'rpm'       : 0x40,
               'pedalA'    : 0x20,
               'pedalB'    : 0x10,
               'EGR_duty'  : 0x08
            },
            'mask_size' : 2 # Mask encoded over 2 bytes
         }
      }

      values = {'IAC_pintle': 0x07, 'rpm': 0x1234, 'pedalA': 0x4, 'pedalB' : 0x5,  'EGR_duty': 0x99}
      req = InputOutputControlByIdentifier.make_request(0x155, values=values, masks=['IAC_pintle', 'pedalA'], ioconfig=ioconfig)

-------

.. _LinkControl:

LinkControl (0x87)
--------------------------------------

.. automethod:: udsoncan.services.LinkControl.make_request
.. automethod:: udsoncan.services.LinkControl.interpret_response

.. autoclass:: udsoncan.services::LinkControl.ResponseData
   :members: 

.. autoclass:: udsoncan.services::LinkControl.ControlType
   :members: 
   :undoc-members:
   :member-order: bysource

-------

.. _ReadDataByIdentifier:

ReadDataByIdentifier (0x22)
--------------------------------------

.. automethod:: udsoncan.services.ReadDataByIdentifier.make_request
.. automethod:: udsoncan.services.ReadDataByIdentifier.interpret_response

.. autoclass:: udsoncan.services::ReadDataByIdentifier.ResponseData
   :members: 

.. note:: This service does not have subfunctions

**Example of DidConfig**

.. code-block:: python

   didconfig = {
      0x1111 : '<H', # Strings are processed by struct.unpack
      0x2222 : MyCustomDidCodec, # Inherits the udsoncan.DidCodec,
      0x3333 : MyCustomDidCodec(param1='hello') # Instance can also be provided
      0x4444 : dict(key1='val1', key2='val2', codec=MyCustomDidCodec} # If dict is given, a key named "codec" will be searched for
   }

-------

.. _ReadDTCInformation:

ReadDTCInformation (0x19)
--------------------------------------

.. automethod:: udsoncan.services.ReadDTCInformation.make_request
.. automethod:: udsoncan.services.ReadDTCInformation.interpret_response

.. autoclass:: udsoncan.services::ReadDTCInformation.ResponseData
   :members: 

.. autoclass:: udsoncan.services::ReadDTCInformation.Subfunction
   :members: 
   :undoc-members:
   :member-order: bysource

make_request() parameters per subfunction
#########################################

.. raw:: html

   <div style='overflow-x:scroll; margin-bottom:24px'>

.. list-table:: make_request() parameters per subfunction
   :header-rows: 1

   * - subfunction
     - status_mask
     - severity_mask
     - dtc
     - snapshot_record_number
     - extended_data_record_number

   * - reportNumberOfDTCByStatusMask
     - Yes
     -  
     -  
     -  
     -  

   * - reportDTCByStatusMask
     - Yes
     - 
     - 
     - 
     - 

   * - reportDTCSnapshotIdentification
     -  
     -  
     -  
     -  
     -  

   * - reportDTCSnapshotRecordByDTCNumber
     -  
     -  
     - Yes
     - Yes
     -  

   * - reportDTCSnapshotRecordByRecordNumber
     -  
     -  
     -  
     - Yes
     -  

   * - reportDTCExtendedDataRecordByDTCNumber
     - 
     - 
     - Yes
     - 
     - Yes

   * - reportNumberOfDTCBySeverityMaskRecord
     - Yes
     - Yes
     -  
     -  
     -  

   * - reportDTCBySeverityMaskRecord
     - Yes
     - Yes
     -  
     -  
     -  

   * - reportSeverityInformationOfDTC
     - 
     - 
     - Yes
     - Yes
     - 

   * - reportSupportedDTCs
     -  
     -  
     -  
     -  
     -  

   * - reportFirstTestFailedDTC
     -  
     -  
     -  
     -  
     -  

   * - reportFirstConfirmedDTC
     -  
     -  
     -  
     -  
     -  

   * - reportMostRecentTestFailedDTC
     -  
     -  
     -  
     -  
     -  

   * - reportMostRecentConfirmedDTC
     -  
     -  
     -  
     -  
     -  

   * - reportMirrorMemoryDTCByStatusMask
     - Yes
     -  
     -  
     -  
     -  

   * - reportMirrorMemoryDTCExtendedDataRecordByDTCNumber
     - 
     - 
     - Yes
     - 
     - Yes

   * - reportNumberOfMirrorMemoryDTCByStatusMask
     - Yes
     -  
     -  
     -  
     -  

   * - reportNumberOfEmissionsRelatedOBDDTCByStatusMask
     - Yes
     -  
     -  
     -  
     -  

   * - reportEmissionsRelatedOBDDTCByStatusMask
     - Yes
     -  
     -  
     -  
     -  

   * - reportDTCFaultDetectionCounter
     -  
     -  
     -  
     -  
     -  

   * - reportDTCWithPermanentStatus
     -  
     -  
     -  
     -  
     -  

.. raw:: html

   </div>


interpret_response() parameters per subfunction
###############################################

.. raw:: html

   <div style='overflow-x:scroll; margin-bottom:24px'>

.. list-table:: interpret_response() parameters per subfunction
   :header-rows: 1

   * - subfunction
     - extended_data_size
     - tolerate_zero_padding
     - ignore_all_zero_dtc
     - dtc_snapshot_did_size
     - didconfig

   * - reportNumberOfDTCByStatusMask
     - 
     - 
     - 
     - 
     - 

   * - reportDTCByStatusMask
     - 
     - Yes
     - Yes
     - 
     - 

   * - reportDTCSnapshotIdentification
     - 
     - Yes
     - Yes
     - 
     - 

   * - reportDTCSnapshotRecordByDTCNumber
     - 
     - Yes
     - 
     - Yes
     - Yes

   * - reportDTCSnapshotRecordByRecordNumber
     - 
     - Yes
     - 
     - Yes
     - Yes

   * - reportDTCExtendedDataRecordByDTCNumber
     - Yes
     - Yes
     - 
     - 
     - 

   * - reportNumberOfDTCBySeverityMaskRecord
     - 
     - 
     - 
     - 
     - 

   * - reportDTCBySeverityMaskRecord
     - 
     - Yes
     - Yes
     - 
     - 

   * - reportSeverityInformationOfDTC
     - 
     - Yes
     - Yes
     - 
     - 

   * - reportSupportedDTCs
     - 
     - Yes
     - Yes
     - 
     - 

   * - reportFirstTestFailedDTC
     - 
     - Yes
     - Yes
     - 
     - 

   * - reportFirstConfirmedDTC
     - 
     - Yes
     - Yes
     - 
     - 

   * - reportMostRecentTestFailedDTC
     - 
     - Yes
     - Yes
     - 
     - 

   * - reportMostRecentConfirmedDTC
     - 
     - Yes
     - Yes
     - 
     - 

   * - reportMirrorMemoryDTCByStatusMask
     - 
     - Yes
     - Yes
     - 
     - 

   * - reportMirrorMemoryDTCExtendedDataRecordByDTCNumber
     - Yes
     - Yes
     - 
     - 
     - 

   * - reportNumberOfMirrorMemoryDTCByStatusMask
     - 
     - 
     - 
     - 
     - 

   * - reportNumberOfEmissionsRelatedOBDDTCByStatusMask
     - 
     - 
     - 
     - 
     - 

   * - reportEmissionsRelatedOBDDTCByStatusMask
     - 
     - Yes
     - Yes
     - 
     - 

   * - reportDTCFaultDetectionCounter
     - 
     - Yes
     - Yes
     - 
     - 

   * - reportDTCWithPermanentStatus     
     - 
     - Yes
     - Yes
     - 
     - 

.. raw:: html

   </div>

-------

.. _ReadMemoryByAddress:

ReadMemoryByAddress (0x23)
--------------------------------------

.. automethod:: udsoncan.services.ReadMemoryByAddress.make_request
.. automethod:: udsoncan.services.ReadMemoryByAddress.interpret_response

.. autoclass:: udsoncan.services::ReadMemoryByAddress.ResponseData
   :members: 

.. note:: This service does not have subfunctions

-------

.. _RequestDownload:

RequestDownload (0x34)
--------------------------------------

.. automethod:: udsoncan.services.RequestDownload.make_request
.. automethod:: udsoncan.services.RequestDownload.interpret_response

.. autoclass:: udsoncan.services::RequestDownload.ResponseData
   :members: 

.. note:: This service does not have subfunctions

-------

.. _RequestTransferExit:

RequestTransferExit (0x37)
--------------------------------------

.. automethod:: udsoncan.services.RequestTransferExit.make_request
.. automethod:: udsoncan.services.RequestTransferExit.interpret_response

.. autoclass:: udsoncan.services::RequestTransferExit.ResponseData
   :members: 

.. note:: This service does not have subfunctions

-------

.. _RequestUpload:

RequestUpload (0x35)
--------------------------------------

.. automethod:: udsoncan.services.RequestUpload.make_request
.. automethod:: udsoncan.services.RequestUpload.interpret_response

.. autoclass:: udsoncan.services::RequestUpload.ResponseData
   :members: 

.. note:: This service does not have subfunctions

-------

.. _RoutineControl:

RoutineControl (0x31)
--------------------------------------

.. automethod:: udsoncan.services.RoutineControl.make_request
.. automethod:: udsoncan.services.RoutineControl.interpret_response

.. autoclass:: udsoncan.services::RoutineControl.ResponseData
   :members: 

.. autoclass:: udsoncan.services::RoutineControl.ControlType
   :members: 
   :undoc-members:
   :member-order: bysource

-------

.. _SecurityAccess:

SecurityAccess (0x27)
--------------------------------------

.. automethod:: udsoncan.services.SecurityAccess.make_request
.. automethod:: udsoncan.services.SecurityAccess.interpret_response

.. autoclass:: udsoncan.services::SecurityAccess.ResponseData
   :members: 

.. autoclass:: udsoncan.services::SecurityAccess.Mode
   :members: 
   :undoc-members:
   :member-order: bysource

.. note:: The ``level`` that act as the subfunction can range from 1 to 0x7E. The LSB is a flag indicating the type of request. 
   When the LSB is set to 1, the request is a RequestSeed message, when it is set to 0, the request is a SendKey message. 
   This leaves 6 effective bits allowing 63 security levels.

   Example : 

   - 01: RequestSeed, 02 SendKey that goes with 01.
   - 03: RequestSeed, 04 SendKey that goes with 03. 
   - etc

-------

.. _TesterPresent:

TesterPresent (0x3E)
--------------------------------------

.. automethod:: udsoncan.services.TesterPresent.make_request
.. automethod:: udsoncan.services.TesterPresent.interpret_response

.. autoclass:: udsoncan.services::TesterPresent.ResponseData
   :members: 

.. note:: TesterPresent subfunction is always 0

-------

.. _TransferData:

TransferData (0x36)
--------------------------------------

.. automethod:: udsoncan.services.RoutineControl.make_request
.. automethod:: udsoncan.services.RoutineControl.interpret_response

.. autoclass:: udsoncan.services::RoutineControl.ResponseData
   :members: 

.. note:: TransferData subfunction is an incrementing counter, not a constant value.

-------

.. _WriteDataByIdentifier:

WriteDataByIdentifier (0x2E)
--------------------------------------

.. automethod:: udsoncan.services.WriteDataByIdentifier.make_request
.. automethod:: udsoncan.services.WriteDataByIdentifier.interpret_response

.. autoclass:: udsoncan.services::WriteDataByIdentifier.ResponseData
   :members: 

.. note:: This service does not have subfunctions

-------

.. _WriteMemoryByAddress:

WriteMemoryByAddress (0x3D)
--------------------------------------

.. automethod:: udsoncan.services.WriteMemoryByAddress.make_request
.. automethod:: udsoncan.services.WriteMemoryByAddress.interpret_response

.. autoclass:: udsoncan.services::WriteMemoryByAddress.ResponseData
   :members: 

.. note:: This service does not have subfunctions

-------

.. _DynamicallyDefineDataIdentifier:

DynamicallyDefineDataIdentifier (0x2C)
--------------------------------------

.. warning:: Not implemented

-------

.. _SecuredDataTransmission:

SecuredDataTransmission (0x84)
--------------------------------------

.. warning:: Not implemented

-------

.. _ResponseOnEvent:

ResponseOnEvent (0x86)
--------------------------------------

.. warning:: Not implemented

-------

.. _ReadScalingDataByIdentifier:

ReadScalingDataByIdentifier (0x24)
--------------------------------------

.. warning:: Not implemented

-------

.. _ReadDataByPeriodicIdentifier:

ReadDataByPeriodicIdentifier (0x2A)
--------------------------------------

.. warning:: Not implemented

