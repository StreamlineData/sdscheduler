# Scheduler

``sdscheduler`` is a flexible python library for building your own cron-like system to schedule jobs, which is to run a tornado process to serve REST APIs and a web ui.



## Table of contents
  
  * [Key Abstractions](#key-abstractions)
  * [Try it NOW](#try-it-now)
  * [How to build Your own cron-replacement](#how-to-build-your-own-cron-replacement)
    * [Install sdscheduler](#install-sdscheduler)
    * [Three things](#three-things)
    * [Reference Implementation](#reference-implementation)   
  * [Contribute code to sdscheduler](#contribute-code-to-sdscheduler)
  * [REST APIs](#rest-apis)
  * [Web UI](#web-ui)

## Key Abstractions

* [CoreScheduler](https://github.com/StreamlineData/sdscheduler/tree/master/sdscheduler/corescheduler): encapsulates all core scheduling functionality, and consists of:
  * [Datastore](https://github.com/StreamlineData/sdscheduler/tree/master/sdscheduler/corescheduler/datastore): manages database connections and makes queries; could support Postgres, MySQL, and sqlite.
    * Job: represents a schedule job and decides how to run a paricular job.
    * Execution: represents an instance of job execution.
    * AuditLog: logs when and who runs what job.
  * [ScheduleManager](https://github.com/StreamlineData/sdscheduler/blob/master/sdscheduler/corescheduler/scheduler_manager.py): access Datastore to manage jobs, i.e., schedule/modify/delete/pause/resume a job.
* [Server](https://github.com/StreamlineData/sdscheduler/tree/master/sdscheduler/server): a tornado server that runs ScheduleManager and provides REST APIs and serves UI.
* [Web UI](https://github.com/StreamlineData/sdscheduler/tree/master/sdscheduler/static): a single page HTML app; this is a default implementation.

Note: ``corescheduler`` can also be used independently within your own service if you use a different Tornado server / Web UI.

## Try it NOW

From source code:

    git clone https://github.com/StreamlineData/sdscheduler.git
    cd sdscheduler
    make simple

Or use docker:

    docker run -it -p 8888:8888 wenbinf/sdscheduler
    
Open your browser and go to [localhost:8888](http://localhost:8888). 

## How to build Your own cron-replacement

### Install sdscheduler
Using pip (from GitHub repo)

    #
    # Put this in requirements.txt, then run
    #    pip install -r requirements.txt
    #

    # If you want the latest build
    git+https://github.com/StreamlineData/sdscheduler.git#egg=sdscheduler

    # Or put this if you want a specific commit
    git+https://github.com/StreamlineData/sdscheduler.git@5843322ebb440d324ca5a66ba55fea1fd00dabe8

    # Or put this if you want a specific tag version
    git+https://github.com/StreamlineData/sdscheduler.git@v0.1.0#egg=sdscheduler
    
    #
    # Run from command line
    #

    pip install -e git+https://github.com/StreamlineData/sdscheduler.git#egg=sdscheduler

(We'll upload the package to PyPI soon.)

### Three things

You have to implement three things for your scheduler, i.e., ``Settings``, ``Server``, and ``Jobs``.

**Settings**

In your implementation, you need to provide a settings file to override default settings (e.g., [settings in simple_scheduler](https://github.com/StreamlineData/sdscheduler/blob/master/simple_scheduler/settings.py)). You need to specify the python import path in the environment variable ``SDSCHEDULER_SETTINGS_MODULE`` before running the server.

All available settings can be found in [default_settings.py](https://github.com/StreamlineData/sdscheduler/blob/master/sdscheduler/default_settings.py) file.

**Server**

You need to have a server file to import and run ``sdscheduler.server.server.SchedulerServer``.

**Jobs**

Each job should be a standalone class that is a subclass of ``sdscheduler.job.JobBase`` and put the main logic of the job in ``run()`` function.

After you set up ``Settings``, ``Server`` and ``Jobs``, you can run the whole thing like this:

    SDSCHEDULER_SETTINGS_MODULE=simple_scheduler.settings \
    PYTHONPATH=.:$(PYTHONPATH) \
		    python simple_scheduler/scheduler.py
		  
**Install dependencies**

    # Each time we introduce a new dependency in setup.py, you have to run this
    make install

**Run unit tests**

    make test
    
**Clean everything and start from scratch**
    
    make clean


## REST APIs

Please see [README.md in sdscheduler/server/handlers](https://github.com/StreamlineData/sdscheduler/blob/master/sdscheduler/server/handlers/README.md).



    
