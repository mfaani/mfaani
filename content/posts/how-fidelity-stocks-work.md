---
title: "How (Fidelity) Stocks Work?"
date: 2022-03-02T21:50:44-05:00
categories: ['Personal Finanace']
tags: ['Fidelity', 'Stocks', 'Retirement', 'RSU', 'ESPP', 'SOP']
---
I know this blog is mainly an engineering blog, but this is something I struggled to learn and thought blogging my findings would be helpful for my friends who want to learn about their investments or evaluate and compare offers. 

The following are a result of my findings after an hour long phone call with Fidelity representitive. I opened the chat section in my browser and asked to speak with someone. I got the following phone number `800-544-9354`. I was waiting on the line for about 7-8 minutes. The conversation was great. After 3 yrs of being an employee I finally got answers. Please note I made some changes to the actual numbers. The screenshots are just for demonstration purposes. 

So at my company, I get:
- Restricted Stock options (RSU or RS)
- Stock option Plan (SOP)
- 401k

But the 'account list' on the left is slightly vague. It looks like this: 
!["account list"](/account-list.png "Fidelity Account List. It kinda remains there for a lot of screens you go to.")

And I was never able to tell how much I actually currently have if I was to leave the company today and how much of it is unvested and will be lost if I leave. 

### Quick breakdown

```
- Individual: All your vested or bought stocks
    - vested (by RSUs)
    - bought (by ESPP)
- Retirement account
- Stocks that are granted but not vested yet. 
    - SOP 
    - ESPP
    - RSU

The Individual sections/accounts are **already** vested and taxed. 
The Retirement account is tax-defferred. It's all yours i.e. no vesting, but you'll end up paying taxes when you cash them. 
The stocks section is the portion of your stocks/options that isn't vested. As it's only been granted. 
```

**Note:** Stocks are granted at their designated time. Then they need to get Distributed and vested. 
Once they get granted, your distribution time table depending on your company will be like:
- 15% first year distributed.
- 15% second year distributed.
- 15% third year distributed.
- 15% forth year distributed.
- 40% fifth year distributed.

or 

- 25% first year distributed.
- 25% second year distributed.
- 25% third year distributed.
- 25% forth year distributed.

Either on the same day of distribution or 3 days later, the distributed stocks will get finally vested. For the most part distributed and vested are 100% the same day. 

To see the distribution plan/table, follow the arrows sections: 

!["RSU Plan Summary"](/RSU-plan-summary.png "Go to this section to see what your distribution table")

Sample distribution:

To see in this view, make sure you've selected **In Table Format**.

!["RSU in Table format"](/RSU-in-table-format.png "RSU in table format - Helps you see how far are you in the vesting of each year's stock that was granted to you before. Anything Distributed is vested.")

If you want to see the your the timeline of the vesting of a specific grant, then from the above screen: 
- Select the 'Account & Info' dropdown
- Then select the 'Transaction history' dropdown. 

You'll get landed to such a page: 

Adjust the 'Transaction Date' and 'Grant Date' if needed and hit Apply. 

!["RSU Transaction history"](/RSU-transaction-history.png "With regards to a specific grant, lists all the vestings that has happened so far.")

## Questions: 

### Why is my stock value far less than my actual RSUs? 

Because you pay income taxes when they get vested.
And whenver you sell the vested stocks then you get taxed for capital gains (profit or loss) between the sell price and vesting price.

e.g.
You're granted a single stock at $200 at 2020
It gets vested at $400 at 2021. 
You sell it for $500 at 2025. 

For tax purposes, the grant price isn't important.
In 2021 you'll $400 as taxable income.
In 2025, you'll have $100 as an increase in your capital gains tax. ($500 - $400 = $100)

### Do I have to file taxes for each year for my stocks?

- Vesting: Whenever it vests, very likely it's already included in your W2 and you get taxed as income tax. 
- Cashing: But if you're selling then stocks, then you just have to pay capital gains tax.

### Should I sell my stocks now? 

So if you sell your stocks today, then whatever profit you have will be considered in this year's taxes. Typically when you're working you already have like ~$100,000 as your salary, and if you sell your stocks you're just adding more to your income. More income might get into a higher tax bracket.

But if you don't cash on it until you're retired, then you're selling it in a year where your income is ~$0 (because you don't have any income salary). So your tax bracket is lower. 

### What's an SOP? 

From [here](https://smartasset.com/investing/how-do-stock-options-work):
> Companies can grant them to employees, contractors, consultants and investors. These options, which are contracts, give an employee the right to buy, or exercise, a set number of shares of the company stock at a preset price, also known as the grant price.

I honestly don't know what it is. Think of it more like a chance to buy stocks of your company for a preset price at a future date. This can be advantageuous to you if the market price might be much higher than the preset price.

### How much time do I have to exercise my SOPs?

Typically it's good for 10 yrs after the grant date while you're at the company.

When you leave it's 90 days. But companies are different. Also every individual's contract is different. So ask your HR department about how much time you have to exercise it after you've left the company.

You'd usually exercise it when your stock is doing good. 

### Is there a way for me to see how much I get if I exercise my SOPs? 

Go to your Stock option Plan section and open the exercise calculator:

![SOP-page](/SOP-exercise-calculator.png "From the drop down select exercise calculator")

Then put in the number of options you want to exercise along with the current stock price: 

![SOP-exercise-calculator](/SOP-exercise-calculator.png "Add the number of stocks and current stock price and hit Estimate (or update)")

So if the Stock options were granted when stock price was $42.52 and now the current prices are $85.00 then if I exercise 500 options I'll end up with 150 stocks. 
If I change the current price to $43.00 then I'll end up with only 2 shares.

To be honest, I just know how to use calculator. I don't know why it's calculated this way...

## Tax Brackets 2021
#### Income tax 

From [nerd wallet](https://www.nerdwallet.com/article/taxes/federal-income-tax-brackets)

![income tax 2021](/income-tax-brackets.png "This is for married filling jointly. For others see the link above")

#### Capital Gain

![capital gain tax 2021](/capital-gain-tax-brackets.png "")

Note Tax brackets may change every year/president.

### What happens if I die? What should I do to make sure everything will get passed along correctly? 

I'll ask and update the post when I find out. 

## Summary

The Fidelity gives you too many ways to see things. It gets confusing. But if you learn of [this structure mentioned above](https://mfaani.com/posts/how-fidelity-stocks-work/#quick-breakdown) and get to know to use the table format, then things will become easier.