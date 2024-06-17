.. container:: cell markdown

   .. rubric:: PyCSI Basic Functions
      :name: pycsi-basic-functions

   *This is a Jupyter Notebook file, make sure to run all the cells in
   the presented order to avoid any errors*
   This tutorial details the basic classes and methods of ``PyCSI``.
   This example uses an ETABS model; however, connecting to a SAP2000 or
   SAFE model is done by changing only the ETABSmodel class for the
   desired software.

.. container:: cell code

   .. code:: python

      # Python packages imports
      import os
      import sys

      ###################################################################
      # The following lines are only necessary for these tutorials
      from pathlib import Path
      pyCSI_dir = str(Path(os.getcwd()).parents[2])
      os.chdir((pyCSI_dir))
      ###################################################################

      # PyCSI import
      import pyCSI

.. container:: cell markdown

   .. rubric:: Connecting to an ETABS model
      :name: connecting-to-an-etabs-model

.. container:: cell markdown

   .. rubric:: Instantiate model class
      :name: instantiate-model-class

   To start the connection process, you must create an instance of the
   ETABSmodel class.
   Instantiating with no arguments will connect to the ETABS version in
   the registry.

.. container:: cell code

   .. code:: python

      default_model = pyCSI.ETABSModel()

.. container:: cell markdown

   You can also provide the version argument to connect to an specific
   version of the software

.. container:: cell code

   .. code:: python

      VERSION = 21
      version_specific_model = pyCSI.ETABSModel(version=VERSION)

.. container:: cell markdown

   This example will use the v21 model

.. container:: cell code

   .. code:: python

      model = default_model

.. container:: cell markdown

   .. rubric:: Connect to a model
      :name: connect-to-a-model

   The ETABSModel class has provides different forms to connect to an
   ETABS model.

   .. rubric:: Connecting to an active model
      :name: connecting-to-an-active-model

   The ``.get_model()`` method will try to connect to an active model.
   If no active model is found, an ``APIConnectionError`` is raised.

   The following code tries to connect to an active model, if there is
   no active model the ``APIConnectionError`` is caught and the
   open_file flag is set to ``True``.

   If the connection is successful, it checks that the name of the model
   matches the example model. If it does not match, then the open_file
   flag is also set to ``True``.

.. container:: cell code

   .. code:: python

      # example_model location 
      # DO NOT MODIFY THE FOLLOWING LINES
      EXAMPLE_LOCATION = os.sep.join([pyCSI_dir, 'docs', 'tutorials', 'resources', 'example_model'])
      EXAMPLE_NAME = 'pyCSI_example - v' + str(VERSION) + '.EDB'
      EXAMPLE_FILE = os.sep.join([EXAMPLE_LOCATION, EXAMPLE_NAME])
      open_file = False  # Flag to be used in the next step

      try:
          # Calling get_model() with no arguments connects to an active model
          model.get_model()
          file_name = model.get_file_name()  # Get the name of the connected model

          # Check that the connected model is the pyCSI_example - v21.EDB file
          # If not, set the flag to open the model in the next step
          if not file_name == EXAMPLE_NAME:
              print('Not connected to Example Model')
              open_file = True
              
              
          
      except pyCSI.APIConnectionError:
          # If no active model is found, an Attribute error is raised
          # Set the flag to open the model in the next step
          print('No active model found')
          open_file = True
          

.. container:: cell markdown

   .. rubric:: Create a new window and open an existing model
      :name: create-a-new-window-and-open-an-existing-model

   If you want to create a new window of the program, use the
   ``.get_model(active_model[, file_location])`` method and specify the
   argument ``active_model = False``. You can also provide a file
   location to open an existing model.

   The following code will create a new window and open the example
   model, only if the open_file flag was set to ``True`` in the last
   step

.. container:: cell code

   .. code:: python

      if open_file:
          print('Opening example model')
          model.get_model(active_model=False, file_location=EXAMPLE_FILE)
          

.. container:: cell markdown

   .. rubric:: Model Information
      :name: model-information

   ``PyCSI`` provides functions to access basic model information.

   .. rubric:: File information
      :name: file-information

   Use the following methods to access the file name and file location

.. container:: cell code

   .. code:: python

      # Get the name of the connected model
      file_name = model.get_file_name()
      print(f'{file_name=}', end='\n\n')

      # Specify include_path argument to include the full path to the model
      full_file_name = model.get_file_name(include_path=True)
      print(f'{full_file_name=}', end='\n\n')

      # Use .get_file_path to get only the path of the connected model
      file_path = model.get_file_path()
      print(f'{file_path=}', end='\n\n')

.. container:: cell markdown

   .. rubric:: Load Cases, Load Patterns and Load Combinations
      :name: load-cases-load-patterns-and-load-combinations

   The following methods return information of the Load Cases, Load
   Patterns and Load Combinations defined in the model.

.. container:: cell code

   .. code:: python

      # Get all load cases defined in the model
      load_cases = model.get_load_cases()  
      print(f'{load_cases=}', end='\n\n')

      # Get all load patterns defined in the model
      load_patterns = model.get_load_patterns()  
      print(f'{load_patterns=}', end='\n\n')

      # Get all load combinations defined in the model
      load_combos = model.get_load_combos()
      print(f'{load_combos=}', end='\n\n')

.. container:: cell markdown

   .. rubric:: Model Properties
      :name: model-properties

   ``PyCSI`` gives access to different model properties, that allow to
   read and modify some of the model states.

.. container:: cell markdown

   .. rubric:: Locking and unlocking the model
      :name: locking-and-unlocking-the-model

   The lock state of the model can be modified through the ``.lock``
   property. This Boolean represents the lock/unlocked state of the
   model. Set this property to ``True`` or ``False`` to modify the lock
   state of the model.

.. container:: cell code

   .. code:: python

      print('Locking the model:')
      model.lock = True # Locks the model. This is the same as clicking the lock button in the GUI
      print(f'{model.lock=}', end='\n\n')

.. container:: cell code

   .. code:: python


      print('Unlocking the model:')
      model.lock = False
      print(f'{model.lock=}')

.. container:: cell markdown

   .. rubric:: Model Units
      :name: model-units

   ETABS stores the model units as integers. Each integer represents a
   different unit. To avoid the need of memorizing these integers,
   enumerator classes are provided to aid with the units assignment
   functions.

   Import these classes from PyCSI:

.. container:: cell code

   .. code:: python

      from pyCSI import TemperatureUnit
      from pyCSI import LengthUnit
      from pyCSI import ForceUnit

.. container:: cell markdown

   The ``.force_unit``, ``.length_unit`` and ``.temperature_unit``
   properties lets you access and modify the current units of the model

.. container:: cell code

   .. code:: python

      # Reading and changing the units of the model using the specific property
      model.force_unit = ForceUnit.TONF # See ForceUnit.help() for available units
      print(f'{model.force_unit=}', end = '\n')

      model.length_unit = LengthUnit.IN # See LengthUnit.help() for available units
      print(f'{model.length_unit=}', end = '\n')

      model.temperature_unit = TemperatureUnit.FAHRENHEIT # See TemperatureUnit.help() for available units
      print(f'{model.temperature_unit=}', end = '\n')

.. container:: cell markdown

   You can also use the
   ``.set_units([force_unit, length_unit, temperature_unit])`` method to
   specify all the output units in a single call. If any argument is not
   provided the default value is assumed

   **Default values are: KIP, FEET and FAHRENHEIT**

.. container:: cell code

   .. code:: python

      model.set_units(force_unit=ForceUnit.LBS, length_unit=LengthUnit.IN)  # Set model units

      print(f'{model.force_unit=}', end = '\n')
      print(f'{model.length_unit=}', end = '\n')
      print(f'{model.temperature_unit=}', end = '\n')

.. container:: cell markdown

   .. rubric:: Window Visibility
      :name: window-visibility

   The ``.visible`` property allows to modify the visibility of the
   current window.
   When a model is hidden, it is not visible either on the screen or in
   the Windows Taskbar, therefore all operations and modifications of
   the model are made in the background, with no update to the software
   graphics.

.. container:: cell code

   .. code:: python

      model.visible = False
      print('Window is hidden')

.. container:: cell code

   .. code:: python

      model.visible = True
      print('Window is visible')

.. container:: cell markdown

   .. rubric:: File Property
      :name: file-property

   The ``.file`` property gives access to the File Menu commands. The
   following functions are available:

.. container:: cell markdown

   .. rubric:: Save model
      :name: save-model

   The ``.file.save()`` method saves the model with the current name and
   location.

.. container:: cell code

   .. code:: python

      model.file.save() # Save current model
      print(f'Model {model.get_file_name()} saved!')

.. container:: cell markdown

   .. rubric:: Save model in a different location
      :name: save-model-in-a-different-location

   Provide the arguments ``file_name`` or ``path`` to the
   ``.file.save()`` method to save the model with a new name or a
   different folder, respectively.

.. container:: cell code

   .. code:: python

      # Get filename and location for backup model
      new_file_name = EXAMPLE_NAME[:-4] + '-backup.EDB'
      new_location = os.sep.join([EXAMPLE_LOCATION, '_backup'])

      try:
          # Create backup folder
          os.mkdir(new_location)
          print('Creating backup folder')
      except FileExistsError:
          # If backup folder already exists, do nothing
          print('Backup folder already exists')

      # Save model using new file name and location
      print('Saving model backup')
      model.file.save(file_name=new_file_name, path = new_location)

.. container:: cell markdown

   .. rubric:: Start a new model
      :name: start-a-new-model

   To start a new blank model use the ``.file.new_model()``.

   **Make sure to save your model before running this method**

.. container:: cell code

   .. code:: python

      print('Starting new model')
      model.file.new_model()

.. container:: cell markdown

   .. rubric:: Open an existing model
      :name: open-an-existing-model

   To open an existing model in the current window use the
   ``.file.open_file(file)`` method.

   The ``file`` argument must contain the full path and filename of the
   existing model.

.. container:: cell code

   .. code:: python

      print(f'Opening {EXAMPLE_NAME} file')
      model.file.open_file(file=EXAMPLE_FILE)

.. container:: cell markdown

   .. rubric:: Analysis Property
      :name: analysis-property

   The ``.analysis`` property gives access to the Analyze menu commands.
   The following functions are available:

.. container:: cell markdown

   .. rubric:: Set load cases to run
      :name: set-load-cases-to-run

   Use ``.analysis.set_load_cases_to_run()`` to set the load cases to
   Run / Do not run.

.. container:: cell code

   .. code:: python

      model.analysis.set_load_cases_to_run(run=True)  # Set all load cases to Run
      model.analysis.set_load_cases_to_run(run=False)  # Set all load cases to Do not Run

.. container:: cell markdown

   If a list of load cases is provided to the ``load_cases`` argument,
   then the Run/Do not Run flag is set only for these specific load
   cases:

.. container:: cell code

   .. code:: python

      load_cases_to_run = ['Dead', 'Superdead', 'Live', 'Live Roof', 'DX', 'DY']
      model.analysis.set_load_cases_to_run(run=True, load_cases=load_cases_to_run)  # Set specified load cases to Run

.. container:: cell markdown

   .. rubric:: Run analysis
      :name: run-analysis

   Use the ``.analysis.run_analysis()`` method to start the analysis of
   the model.

.. container:: cell code

   .. code:: python

      model.analysis.run_analysis()

.. container:: cell markdown

   .. rubric:: Delete analysis results
      :name: delete-analysis-results

   Once the model is run, use the
   ``.analysis.delete_results([load_cases])`` method to delete results
   of specified load cases.

   If the ``load_cases`` argument is not provided, then the results for
   all load cases will be deleted.

.. container:: cell code

   .. code:: python

      model.analysis.delete_results(load_cases=['DY'])

.. container:: cell markdown

   .. rubric:: Close the model
      :name: close-the-model

   Finally, to close a model use the
   .\ ``close_application([save_model])`` method.

.. container:: cell code

   .. code:: python

      # Set save_model to True to save before closing, default is True
      print('Closing model')
      model.close_application(save_model=False)

.. container:: cell markdown

   .. rubric:: Next -> `Table Operations <3_Table_Operations.ipynb>`__
      :name: next---table-operations

   **REMEMBER to shut down the Kernel before leaving**

   Click ``Kernel -> Shut Down Kernel``

.. container:: cell markdown

   .. rubric:: Contact
      :name: contact

   For questions or comments please reach out to:

   -  Luis Pancardo:
      `lpancardo@degenkolb.com <lpancardo@degenkolb.com>`__\ 
   -  Daniel Gaspar: `dgaspar@degenkolb.com <dgaspar@degenkolb.com>`__\ 
