# Center of Excellence - Capacity Extension solution

![Image environment storage with chart](./ppcoe-screenshot.png)

## Summary

Extension to the Power Platform Center of Excellence Starter Kit. Track database storage targets and actual consumption.

- Track targets/actual on a per-environment basis
- Environment records updated with current DB consumption once daily
- Grid-view reporting to show environments over 80% of target
- Includes charts to show consumption.

> **Note:** This sample is one of a collection of Power Platform samples.
> [Check out the larger list here](../../README.md#Sample-Solutions).

## Deployment

### Prerequisites

- Environment with **Center of Excellence – Core Components** v1.45 or greater deployed

### Solution Deployment

- Import the solution via the Power Apps maker portal -> Solutions area
  - Either:
    - Managed: CoECapacityExtension_x_x_x_x.zip
    - Unmanaged: CoECapacityExtension_x_x_x_x.zip
- Modify the **Power Platform Admin View** app
  - Add the following Environment components
    - Form: **main form**
    - Views: all views with “capacity” in the title
    - Chart: **Environment Data Storage**
  - Deselect the original form from showing the app
    - Form: **Main Environment Form**
  - Save and publish the app

### Update data

- Log into the **Power Platform Admin View** app
- Navigate to the Environments -> **Active Environments with Capacity** view
- Save the data to CRM and wait for the import to refresh.

## Release History

### 2.1.0

- Environment table
  - Added *Rated Consumption* column
  - Adjusted consumed percentage to use *Rated Consumption* column
  - Added *Rated Consumption* to main form
  - Added *Rated Consumption* to capacity views
  - Filtered capacity views to include flag for *hasCds=True*

### 2.0.0

- Added *Log environment DB consumption* cloud Flow
  - Daily recurring trigger
  - Queries Power Platform Admin API, version 2020-10-01 with *Capacity* data preview
  - Updates static value in Environment record
  