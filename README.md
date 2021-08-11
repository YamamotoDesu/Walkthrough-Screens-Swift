# WalkthroughScreens-Swift
![platforms](https://img.shields.io/badge/platforms-iOS-333333.svg)  
<table border="0">
  <tr>
    <th>PageSwiped</th>
    <th>ButtonTapped</th>
  </tr>
  <tr>
    <td><img src="https://github.com/YamamotoDesu/WalkthroughScreens-Swift/blob/main/FoodPin/Gif/RocketSim%20Recording%20-%20iPhone%2012%20-%202021-08-11%2009.38.47.gif" height="400" width="200"></td>
    <td><img src="https://github.com/YamamotoDesu/WalkthroughScreens-Swift/blob/main/FoodPin/Gif/RocketSim%20Recording%20-%20iPhone%2012%20-%202021-08-11%2009.40.08.gif" height="400" width="200"></td>
  </tr>
</table>

## Context  
WalkthroughScreens are useful for displaying information.   
This is an easy introduction about how to use the UIPageViewController class.  
https://developer.apple.com/documentation/uikit/uipageviewcontroller

## Usage
https://github.com/YamamotoDesu/WalkthroughScreens-Swift/blob/main/FoodPin/Storyboard/Onboarding.storyboard  
<img src="https://user-images.githubusercontent.com/47273077/128966584-473c53bd-4e32-48b9-a976-e744f05a4780.png" height="400" width="800">

WalkthroughViewController  
https://github.com/YamamotoDesu/WalkthroughScreens-Swift/blob/main/FoodPin/Controller/WalkthroughViewController.swift  
```swift
class WalkthroughViewController: UIViewController {
    
    @IBOutlet var pageControl: UIPageControl!
    @IBOutlet var nextButton: UIButton! {
        didSet {
            nextButton.layer.cornerRadius = 25.0
            nextButton.layer.masksToBounds = true
        }
    }
    @IBOutlet var skipButton: UIButton!

    @IBAction func skipButton(sender: UIButton) {
        dismiss(animated: true, completion: nil)
    }
    
    //  declare a  walkthroughDelegate  variable to hold the delegate
    var walkthroughPageViewController: WalkthroughPageViewController?
    
    override func viewDidLoad() {
        super.viewDidLoad()
    }
    
    @IBAction func nextButtonTapped(sender: UIButton) {
        if let index = walkthroughPageViewController?.currentIndex {
            switch index {
            case 0...1:
                walkthroughPageViewController?.forwardPage()
            case 2:
                dismiss(animated: true, completion: nil)
            default: break
            }
        }
        updateUI()
    }
    
    // controls the title of the next button and determines whether the skip button should be hidden
    func updateUI() {
        if let index = walkthroughPageViewController?.currentIndex {
            switch index {
            case 0...1:
                nextButton.setTitle("NEXT", for: .normal)
                skipButton.isHidden = false
            case 2:
                nextButton.setTitle("GET STARTED", for: .normal)
                skipButton.isHidden = true
            default: break
            }
            pageControl.currentPage = index
        }
    }
    
    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        let destination = segue.destination
        if let pageViewController = destination as? WalkthroughPageViewController {
            walkthroughPageViewController = pageViewController
            walkthroughPageViewController?.walkthroughDelegate = self
        }
    }

}

// update UI(PageSwiped)
extension WalkthroughViewController: WalkthroughPageViewControllerDelegate
{
    func didUpdatePageIndex(currentIndex: Int) {
        updateUI()
    }
}

```

WalkthroughPageViewController  
https://github.com/YamamotoDesu/WalkthroughScreens-Swift/blob/main/FoodPin/Controller/WalkthroughPageViewController.swift 
```swift

// declare a didUpdatePageIndex method that tells its delegate the current page index(PageSwiped)
protocol WalkthroughPageViewControllerDelegate: class {
    func didUpdatePageIndex(currentIndex: Int)
}

class WalkthroughPageViewController: UIPageViewController {
    
    weak var walkthroughDelegate: WalkthroughPageViewControllerDelegate?
    
    var pageHeadings = ["CREATE YOUR OWN FOOD GUIDE", "SHOW YOU THE LOCATION",
    "DISCOVER GREAT RESTAURANTS"]
    var pageImages = ["onboarding-1", "onboarding-2", "onboarding-3"]
    var pageSubHeadings = ["Pin your favorite restaurants and create your own food guide",
                       "Search and locate your favourite restaurant on Maps",
                       "Find restaurants shared by your friends and other foodies"]
    var currentIndex = 0
    
    // next button tapped
    func forwardPage() {
        currentIndex += 1
        if let nextViewController = contentViewController(at: currentIndex) {
            setViewControllers([nextViewController], direction: .forward, animated: true, completion: nil)
        }
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        
        // assign the delegate of the  UIPageViewControllerDelegate  protocol
        delegate = self
        dataSource = self
        
        // Create the first walkthrough screen
        if let startingViewController = contentViewController(at: 0) {
            setViewControllers([startingViewController], direction: .forward,
                               animated: true, completion: nil)
        }

    }
    
}

extension WalkthroughPageViewController: UIPageViewControllerDataSource {
    
    //  swipe right the image will bring you the next screen(PageSwiped)
    func pageViewController(_ pageViewController: UIPageViewController, viewControllerBefore viewController: UIViewController) -> UIViewController? {
        var index = (viewController as! WalkthroughContentViewController).index
        
        index -= 1
        
        return contentViewController(at: index)
    }
    
    //  swipe left the image will bring you the next screen(PageSwiped)
    func pageViewController(_ pageViewController: UIPageViewController, viewControllerAfter viewController: UIViewController) -> UIViewController? {
        var index = (viewController as! WalkthroughContentViewController).index
        
        index += 1
        
        return contentViewController(at: index)
    }
    
    func contentViewController(at index: Int) -> WalkthroughContentViewController? {
        if index < 0 || index >= pageHeadings.count {
            return nil
        }
        // Create a new view controller and pass suitable data.
        let storyboard = UIStoryboard(name: "Onboarding", bundle: nil)
        if let pageContentViewController = storyboard.instantiateViewController(withIdentifier: "WalkthroughContentViewController") as? WalkthroughContentViewController {
            pageContentViewController.imageFile = pageImages[index]
            pageContentViewController.heading = pageHeadings[index]
            pageContentViewController.subHeading = pageSubHeadings[index]
            pageContentViewController.index = index
            return pageContentViewController
        }
        return nil
    }
}

extension WalkthroughPageViewController: UIPageViewControllerDelegate {

    // check if the transition is completed and find out the current page index(PageSwiped)
    func pageViewController(_ pageViewController: UIPageViewController, didFinishAnimating finished: Bool, previousViewControllers: [UIViewController], transitionCompleted completed: Bool) {
        if completed {
            if let contentViewController = pageViewController.viewControllers?.first as? WalkthroughContentViewController {
                currentIndex = contentViewController.index
                walkthroughDelegate?.didUpdatePageIndex(currentIndex: contentViewController.index)
            }
        }
    }
}

```

WalkthroughContentViewController  
https://github.com/YamamotoDesu/WalkthroughScreens-Swift/blob/main/FoodPin/Controller/WalkthroughContentViewController.swift  
```swift
    @IBOutlet var headingLabel: UILabel! {
        didSet {
            headingLabel.numberOfLines = 0
        }
    }
    @IBOutlet var subHeadingLabel: UILabel! {
        didSet {
            subHeadingLabel.numberOfLines = 0
        }
    }
    @IBOutlet var  contentImageView: UIImageView!

    override func viewDidLoad() {
        super.viewDidLoad()
        headingLabel.text = heading
        subHeadingLabel.text = subHeading
        contentImageView.image = UIImage(named: imageFile)
    }
    
    var index = 0
    var heading = ""
    var subHeading = ""
    var imageFile = ""
```


