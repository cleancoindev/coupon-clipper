# coupon-clipper
Better living through bot bribery

Warning: This is unaudited code. Use it at your own risk. Please exercise some prudence.

# where
`CouponClipperV3` can be found on mainnet at: TODO

A friendly UI has been built by Lewi at [https://esd.coupons/](https://esd.coupons/).

# what
`CouponClipperV3` is a contract that that helps ESD coupon holders and bot writers come together and help each other.

Coupon holders approve the `CouponClipperV3` contract address (via the [`ESDS.approveCoupons` function](https://github.com/emptysetsquad/dollar/blob/master/protocol/contracts/dao/Market.sol#L98)).

Bot writers listen for the [`CouponApproval` events](https://github.com/emptysetsquad/dollar/blob/master/protocol/contracts/dao/Market.sol#L103) to learn which coupon holders have approved the `CouponClipper` contract. They can cross reference these with the `setOffer` events emitted by this contract to see which coupon holders have made offers.

When coupons can be redeemed, bots can call the [`CouponClipperV3.redeem` function](https://github.com/Austin-Williams/coupon-clipper/blob/main/contracts/CouponClipper.sol#L113) to claim any of those user's coupons on their behalf -- taking a fee for their service. The developer gets a "house take" for creating this service, which is a percentage of the proceeds that go to the bots (see the "Improvements Since V2" section below).

TThere is no minimum fee. Users are free to change the fee to any value they want by calling the [`setOffer` function](https://github.com/Austin-Williams/coupon-clipper/blob/main/contracts/CouponClipper.sol#L72) and passing in the number of basis points they want to offer for having their coupons redeemed for them.

The ESD foes directly into the user's accounts. No action is required on behalf of the coupon holders other than approving the `CouponClipper` contract via the [`ESDS.approveCoupons` function](https://github.com/emptysetsquad/dollar/blob/master/protocol/contracts/dao/Market.sol#L98).

# why
Only so many coupons can be redeemed per epoch, so there is often a race to determine who gets to redeem first. Naturally, bots win these races and get to redeem all their own coupons first.

When the bots have claimed all their own coupons, then the "manual users" have a chance.

`CouponClipper` is a way for "manual users" to get the bots to work for them in a trustless fashion.

# contribute
Please see the issues section for ways you can help.

# Improvements Since V2

I've gotten some excellent feedback from V2 and have implemented the suggestions here, along with some nice features I thought of myself.

**For coupon holders** V3 improves on V2 by removing the old minimum on their offers. The old V2 had a minimum 2% offer, which is now gone. With V3, coupon holders can set as low of an offer as they want.

The default fee of 2% has been removed. We no longer make any assumptions about what coupons holders might want to offer.

**For bot runners** V3 improves on V2 by having a lower house take, and by taking a percentage of the _proceeds_ instead of on the total amount of coupons moving through the contract. From the last expansion cycle (as of this writing) the typical offer by coupon holders was between 2% and 6%. In V2, the house took 1% of the _total coupon value moving_ through the clipper contract. This meant the house was taking between 17% and 50% of the proceeds generated through the CouponClipper contract.

In V3 the house no longer takes a percentage of the total coupon value moving through the contract. Instead, a percentage is taken from the _proceeds_ generated by the clipper contract. The house take can be modified (see below) but can never be greater than 15% of the proceeds -- which means bots will take home a much bigger piece of the pie than they did with V2. (At the start, the house take will be set to 10% of the proceeds).

**For the CouponClipper project itself** V3 improves on V2 by making CouponClipper more defensible against copy/paste competitors. Since the house take can be changed to anything from 0% to 15% of the proceeds generated by the contract, it can always be _reduced_ to compete against copy/paste competitors as needed. In this way, CouponClipper has a chance to defend itself against copy/paste competitors that might offer lower fees or more gas efficiency. Competition is good for the end users and for the bot runners, and this change allows CouponClipper to compete more fluidly.

The house fee cannot be *increased* fewer than 15 minutes before an epoch, so you don't have to worry about a malicious owner frontrunning to increase the fee unexpectedly.