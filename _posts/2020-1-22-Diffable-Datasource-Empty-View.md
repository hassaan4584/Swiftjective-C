---
layout: post
tags: ["Tech Notes"]
title: "Empty View With Diffable Datasource"
writtenBy: Jordan Morgan
writtenByTwitter: "https://www.twitter.com/jordanmorgan10"
description: "Empty views are a critical piece of user experience on iOS. Today in tech notes, I explore how this might be done with diffable datasource."
image: /assets/images/logo.png
---

Among my favorite APIs introduced at W.W.D.C. is without a doubt the new diffable data source for both [table and collection views][1]{:target="_blank"}. Replacing a decade old protocol, it brings about a robust way of expressing what should show when, where the truth is and, *finally*, a way to forgo the death trap that is batch updates.

While rewriting the list view of Spend Stack, I immediately become aware of its benefits:

{% twitter https://twitter.com/JordanMorgan10/status/1217622139718774784 %}

As far as user experience, batch updates are far superior to the sledgehammer approach of `reloadData`, so this is a needed step forward. But, that's only part of it. I'm personally a believer in showing "empty view" states, and if you peek into Apple's stock apps - they are too. Here's an example of a bag with no items in the Apple Store app:

{% include lazyLoadImage.html image="../assets/images/empty.jpeg" %}

Previously, I used associated objects and the [Objective-C Funtime to do the same thing][2]{:target="_blank"} in Spend Stack: 

{% include lazyLoadImage.html image="../assets/images/emptySS.jpeg" %}

But hey - I'm being all _swifty_ now, right? So I've been thinking about how to do the same with my new bestie, diffable data source while forgoing swizzling. Here's what I've got so far:

```swift
func createSubscriptions() {
    let nc = NotificationCenter.default

    // Other subs..

    // The one when a list is mutated
    let tvAnim = Int(SSTableViewBatchUpdateAnimation)
    let listCRUD = nc.publisher(for: .listCRUD)
    .compactMap { $0.object as? SSList }
    .debounce(for: .milliseconds(tvAnim), scheduler: RunLoop.main)
    .sink { [unowned self] list in
        self.showEmptyViewIfNeeded()
    }
    
    subscriptions.append(listCRUD)
}
```

This function runs when I initialize my subclassed diffable data source (to provide for things like `tableView(_ tableView: UITableView, moveRowAt sourceIndexPath:, to:)`). When Combine fires off the closure, I simply check if the data is empty:

```swift
func showEmptyViewIfNeeded() {
    guard let view = emptyView, let tv = tableView else { return }
    let shouldShow = snapshot().itemIdentifiers.isEmpty && view.superview == nil

    if shouldShow {
        guard let vc = tv.closestViewController() else { return }
        vc.view.addSubview(view)

        if let constraints = self.emptyViewConstraints {
            view.snp.remakeConstraints { make in
                constraints(make)
            }
        }
        } else {
            view.removeFromSuperview()
        }
    }
```

This all works, but there are some things that need to improve. Notably, there are two core properties on my data source that need to be abstracted out in some fashion:

```swift
// Properties on data source object
var emptyView:UIView?
var emptyViewConstraints:((_ make: ConstraintMaker) -> Void)?

// Then later on, when initializing in a view controller or something similar
dataSource.emptyView = SSEmptyStateView(stateText: ss_Localized("list.vc.empty"))
dataSource.emptyViewConstraints = { [unowned self] make in
    let lg = self.view.safeAreaLayoutGuide
    make.top.equalTo(lg.snp.top)
    make.bottom.equalTo(self.toolBar.snp.top)
    make.centerX.equalTo(lg.snp.centerX)
    make.width.equalTo(lg.snp.width)
}
```

Currently, the empty data view only works with this particular instance. I already know I'll need it for the rest of the app, too. As I mentioned above, in Objective-C I have an `EmptyDataSetDelegate` protocol which is tacked onto any collection or table view via associated objects.

I _could_ do something similar, I suppose. Here are some thoughts so far.

I could use a protocol so other types could do something similar:

```swift
protocol EmptyDataViewProviding {
    var emptyView:UIView? { get set }
    var emptyViewConstraints:((_ make: ConstraintMaker) -> Void)? { get set }
    
    func showEmptyDataView() -> Void
}
```
Plus, with protocol extensions I could vend a default implementation.

Next, I could simply extend the type. But without associated objects, I'd lose the two properties I need that describe the view and how it should be constrained. 
```swift
extension UITableViewDiffableDataSource {
    func showEmptyView() {
        if snapshot().itemIdentifiers.isEmpty {
            // Do stuff
        }
    }
}
```

At first, I attempted this route by trying to include the properties using a property wrapper implementation, but that's not allowed in extensions as far as I can tell:

```swift
extension UITableViewDiffableDataSource {
    @AssociatedObject var:emptyView?
    @AssociatedObject var:emptyViewConstraints:((_ make: ConstraintMaker) -> Void)?

    func showEmptyView() {
        if snapshot().itemIdentifiers.isEmpty {
            // Do stuff
        }
    }
}
```

Or, perhaps plain old object oriented programming is the answer? Simply a base type that others extend. [Krusty][3] would be so disappointed in me though.
```swift
class BaseDiffable : UITableViewDiffableDataSource<SSListTag, SSListItem> {
    var emptyView:UIView?
    var emptyViewConstraints:((_ make: ConstraintMaker) -> Void)?
    
    override func apply(_ snapshot: NSDiffableDataSourceSnapshot<SSListTag, SSListItem>, animatingDifferences: Bool = true, completion: (() -> Void)? = nil) {
        super.apply(snapshot, animatingDifferences:animatingDifferences, completion: completion)
        if snapshot.itemIdentifiers.isEmpty {
            // Show empty view
        }
    }
}
```

I can't decide which I like the most. In the end, my existing Objective-C solution bridges over fine, but if I can avoid swizzling batch updates, I will.

I'll follow up on Twitter with where I end up.

Until next time ✌️.

[1]: https://developer.apple.com/documentation/uikit/views_and_controls/collection_views/
[2]: https://gist.github.com/DreamingInBinary/e4218c00dbeff815e26426af402ca2ad
[3]: https://developer.apple.com/videos/play/wwdc2015/408/