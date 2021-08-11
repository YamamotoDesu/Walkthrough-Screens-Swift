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

WalkthroughViewController
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

extension WalkthroughViewController: WalkthroughPageViewControllerDelegate
{
    func didUpdatePageIndex(currentIndex: Int) {
        updateUI()
    }
}

```


