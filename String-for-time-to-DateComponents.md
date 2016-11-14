## "HH:mm" 형식 String을 DateComponents로 변환하는 함수

NSRegularExpression 으로 "HH:mm" 형식인지 확인하고, "시간"과 "분" 부분과 매칭되는 부분을 찾아 Int로 변환한다.

```swift
import Foundation

extension String {
    
    func range(from nsRange: NSRange) -> Range<String.Index>? {
        guard
            let from16 = utf16.index(utf16.startIndex, offsetBy: nsRange.location, limitedBy: utf16.endIndex),
            let to16 = utf16.index(from16, offsetBy: nsRange.length, limitedBy: utf16.endIndex),
            let from = String.Index(from16, within: self),
            let to = String.Index(to16, within: self) else {
                return nil
        }
        return from..<to
    }
    
    func nsRange(from range: Range<String.Index>) -> NSRange {
        let from = range.lowerBound.samePosition(in: utf16)
        let to = range.upperBound.samePosition(in: utf16)
        
        return NSRange(location: utf16.distance(from: utf16.startIndex, to: from), length: utf16.distance(from: from, to: to))
    }
}

func components(from timeString: String) -> DateComponents? {
    
    let timeStringPattern = "^\\s*(\\d{1,2}):(\\d{1,2})\\s*$"
    let regexForTimeString = try! NSRegularExpression(pattern: timeStringPattern, options: [])
    let fullRange = timeString.startIndex..<timeString.endIndex
    
    guard
        let match = regexForTimeString.firstMatch(in: timeString, options: [], range: timeString.nsRange(from: fullRange)),
        match.numberOfRanges >= 3,
        let hourRange = timeString.range(from: match.rangeAt(1)),
        let minuteRange = timeString.range(from: match.rangeAt(2)) else {
            return nil
    }
    
    guard
        let hour = Int(timeString.substring(with: hourRange)),
        let minute = Int(timeString.substring(with: minuteRange)) else {
            return nil
    }
    
    var components = DateComponents()
    components.hour = hour
    components.minute = minute
    
    return components

}
```

