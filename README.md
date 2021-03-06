# Liquibook

Open source order matching engine from [OCI](http://ociweb.com)

Liquibook provides the low-level components that make up an order matching engine. 

Order matching is the process of accepting buy and sell orders for a security (or other fungible asset) and matching them to allow 
trading between parties who are otherwise unknown to each other.

An order matching engine is the heart of every financial exchange, 
and may be used in many other circumstances including trading non-financial assets, serving as a test-bed for trading algorithms, etc.

In addition to the order matching process itself, Liquibook can be configured
to maintain an "order book" that records the number of open orders and total quantity
represented by those orders at individual price levels.  

   i.e. Symbol XYZ: open buy orders: $53.20 per share: 1203 orders for a total quantity of 150,398 shares.

## Order properties supported by Liquibook.

The orders managed by Liquibook can include the following properties:

* Buy or Sell
* Quantity
* Symbol to represent the asset to be traded
  * Liquibook imposes no restrictions on the symbol.  It is treated as a simple character string.
* Desired price or "Market" to accept the current price defined by the market.
  * Trades will be generated at the specified price or any better price (higher price for sell orders, lower price for buy orders)
* Stop loss price to hold the order until the market price reaches the specified value.
  * This is often referred to as simply a stop price.
* All or None flag to specify that the entire order should be filled or no trades should happen.
* Immediate or Cancel flag to specify that after all trades that can be made against existing orders on the market have been made, the remainder of the order should be canceled.
  * Note combining All or None and Immediate or Cancel produces an order commonly described as Fill or Kill.
  
## Operations on Orders

In addition to submitting orders, traders may also submit requests to cancel or modify existing orders.  (Modify is also know as cancel/replace)
The requests may succeed or fail depending on previous trades executed against the order.
  
## Notifications returned to the application.

Liquibook will notify the application when significant events occur to allow the application to actually execute the trades identified by Liquibook, and to allow the application to publish market data for use by traders.

The notifications generated include:

* Notifications intended for trader submitting an order:
  * Order accepted 
  * Order rejected
  * Order filled (full or partial)
  * Order replaced
  * Replace request rejected
  * Order canceled
  * Cancel request rejected.
* Notifications intended to be published as Market Data
  * Trade
    * Note this should also trigger the application to do what it needs to do to make the trade happen.
  * Security changed
    * Triggered by any event that effects a security
      * Does not include rejected requests.
  * Notification of changes in the order book (if enabled)
    * Order book changed
    * Best Bid or Best Offer (BBO) changed.


## Performance
* Liquibook is written in C++ using modern, high-performance techniques.
  * Benchmark testing (with source included in this repository) show sustained rates of  
__2.0 million__ to __2.5 million__ inserts per second.  See full [performance history](PERFORMANCE.md).

## Works with Your Design
* Allows an application to use smart or regular pointers to orders.
* Compatible with existing order model, 
  * Requires a trivial interface which can be added to or wrapped around an existing Order object.
* Compatible with existing identifiers for securities, accounts, exchanges, orders, fills

##Example
This repository contains two complete example programs.  These programs can be used to evaluate Liquibook to see if it meets your needs.  
They can also be used as models for your application or even incorporated directly into your application thanks to the
liberal license under which Liquibook is distributed.

The examples are:
* Depth feed publisher and subscriber
  * Generates orders that are submitted to Liquibook and publishes the resulting market data.
  * Uses OCI's [QuickFAST](https://www.ociweb.com/products/quickfast/) to publish the market data

* Manual Order Entry
  * Allows orders and other requests to be read from the console or submitted by a script (text file)
  * Submits these to Liquibook.
  * Displays the notifications received from Liquibook to the console or to a log file.

Build Dependencies
------------------

* [MPC](http://www.ociweb.com/products/mpc) for cross-platform builds
* [Assertiv](https://github.com/iamtheschmitzer/assertiv) for unit testing (see note below0
* BOOST (optional) for shared pointer unit testing only
* [QuickFAST](https://www.ociweb.com/products/quickfast/) (optional) for building the example depth feed publisher/subscriber

## Submodule Note

Assertiv is included as a submodule.  After cloning liquibook, you must:

<pre>
> cd liquibook
> git submodule init
> git submodule update
</pre>

## Linux Build Notes

Make sure the $BOOST_ROOT, $QUICKFAST_ROOT (set to liquibook/noQuickFAST if you don't want to use QuickFAST) and $MPC_ROOT environment variables are set, then open a shell

<pre>
$ cd liquibook
$ . env.sh
$ mwc.pl -type make liquibook.mwc
$ make depend
$ make all
</pre>

If you don't have readlink, set the $LIQUIBOOK_ROOT environment variable before running env.sh

## Windows Build Notes
Use the following commands to set up the build environment and create Visual Studio project and solution files.
Note if you are using MinGW or other linux-on-Windows techniques, follow the Linux instructions; however, OCI does not normally test this.

<pre>
> cd liquibook
> copy winenv.bat w.bat #optional if you want to keep the original
                        # note that single character batch file names are ignored in .getignore so the customized
                        # file will not be checked into the git repository.
> edit w.bat            # edit is your choice of text editor
                        # follow the instructions in the file itself.
> w.bat                 # sets and verifies environment variables
> mpc.bat               # generate the visual studio solution and project files.
</pre>

Then in the same window, start Visual Studio from the command line, opening liquibook.sln
<pre>
> liquibook.sln
</pre>
If Windows does not recognize the *.sln file extension as belonging to VisualStudio,
you may need to provide the path to Visual Studio.

This example is the Visual Studio 2010 Express Edition:
<pre>
> "%VS100COMNTOOLS%\..\IDE\VCExpress.exe" liquibook.sln
</pre>

## For any platform

See other [build notes](BUILD_NOTES.md).
