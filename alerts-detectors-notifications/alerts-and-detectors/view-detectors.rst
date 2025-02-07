.. _view-detectors:

************************************
View detectors
************************************



.. meta::
  :description: How to view detector list and individual detectors in Splunk Observability Cloud.

You can view detectors as line items in a list, or individually. When you open an individual detector, you can see also see its rules and settings.

View a list of all detectors
================================

To see a list of existing detectors, open :guilabel:`Detectors & SLOs` page and select the :guilabel:`Detectors` tab.

* By default, detectors are sorted by last updated, with the most recently updated detector at the top. To reverse the sorting order or sort detectors by a different criterion, select the corresponding column header.
* To filter detectors by assigned teams, select the :guilabel:`Team` menu and select or enter the team name you want to find.
* To filter detectors by type, select the :guilabel:`Type` menu. You can filter detectors by the following types:

   * Standard detectors are user-created detectors, including all RUM, APM, and Synthetics detectors.
   * AutoDetect detectors are read-only detectors Splunk Observability Cloud automatically creates when you have supported integrations configured. To learn more, see :ref:`autodetect-intro`.
   * Customized AutoDetect detectors are AutoDetect detectors that have been copied and customized. To learn more, see :ref:`autodetect-customize`.

* To filter detectors by tags, enter the tags you want to find.
* Detectors with active or scheduled muting rules directly applied to them have a muting indicator. If a detector is muted but the muting rule applies only to the detector's properties, the detector doesn't have a muting indicator.


.. _view-related-detectors:

View detectors related to a chart or the Infrastructure Navigator
====================================================================================

When you are looking at the Detector menu for a chart, or in the Infrastructure Navigator, you might see one or more Related Detectors. Making related detectors easy to find helps ensure that everyone using Infrastructure Monitoring in your organization is using the same detectors to monitor the same data.

..
	|openmenu| is defined in conf.py

|openmenu| The following illustration shows two related detectors for this chart. If you hover over a related detector, you see options that let you :ref:`subscribe to the detector<subscribe>` by adding a new notification, open the detector for viewing or editing, or view the alerts triggered by the detector. To learn more, go to :ref:`view-alerts`.

.. image:: /_images/images-detectors-alerts/detectors-related.png
   :width: 50%

View an individual detector
================================================================

There are two charts in the detector view. On the right side, you can see a detailed view. It shows each data point at the native resolution of the detector and represents exactly the datapoints that the detector sees. On the left side, you can see a summary view. It shows a summary of the data over a longer period of time. Because it is a summary, short spikes are not visible. The yellow box controls which part of the summary chart displays in the detail chart. You can see a short-term spike in the detail view by dragging the yellow box to the area where the alert fired.

The Alert Rules tab is open when you open a detector, showing a chart that represents values for the visible signals. The list of detector rules and the number of currently active alerts for each rule are visible. To learn more, see :ref:`view-alerts-within-detector`. For information on creating rules, see :ref:`build-rules` or :ref:`apm-alerts`, depending on which type of detector you are creating.

As with charts, the resolution of data displayed is determined by the chart's time range. The detail view at right displays data at the detector's resolution, that is, the frequency at which the detector evaluates the signal. Any events that have occurred during the detector's time range are shown under the X axis.

.. note:: If a detector contains a SignalFlow tab, you are viewing a detector that created using the API.

   If you are familiar with the API, you can use this tab to view and edit the detector code and make changes to the detector rules. For more information, see :ref:`v2-detector-signalflow`.


View a detector's properties
-----------------------------------

You can see a detector's properties, such as its description and creator, by following these steps:

#. Open the detector.
#. Select the detector's actions menu (|more|), then select :menuselection:`Info`.

This displays the detector's properties, as shown in the illustration.


.. image:: /_images/images-detectors-alerts/detector-info.png
  :width: 90%
  :alt: Detector info panel showing description, creator, and other properties.


