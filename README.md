# Flutter Live Activities

Flutter plugin for Live Activities. Use to create, update and handling action for [DynamicIsland UI] and [Lock screen/banner UI]

1. Add a Widget to the iOS project

2. Edit Runner/Info.plist and live_activity_test/Info.plist

both add:

   ```
   <plist version="1.0">
<dict>
    ...
	<key>NSSupportsLiveActivities</key>
	<true/>
    ...
</dict>
</plist>
```

3. Create a data channel in widget swift file

live_activity_test/live_activity_testLiveActivity.swift

   ```
   import ActivityKit
import SwiftUI
import WidgetKit

// Custom data model
struct TestData {
    var text: String

    init?(JSONData data: [String: String]) {
        self.text = data["text"] ?? ""
    }

    init(text: String) {
        self.text = text
    }
}

// Data channel  <-  Must!
struct FlutterLiveActivities: ActivityAttributes, Identifiable {
    public typealias LiveData = ContentState

    public struct ContentState: Codable, Hashable {
        var data: [String: String]
    }

    var id = UUID()
}

@available(iOSApplicationExtension 16.1, *)
struct live_activity_testLiveActivity: Widget {
    var body: some WidgetConfiguration {
        // Binding
        ActivityConfiguration(for: FlutterLiveActivities.self) { context in

            // Lock screen/banner UI goes here

            // Json to model
            let data = TestData(JSONData: context.state.data)

            // UI
            VStack {
                Text(data?.text ?? "")
            }
            .activityBackgroundTint(Color.cyan)
            .activitySystemActionForegroundColor(Color.black)
        } dynamicIsland: { context in
            // Json to model
            let data = TestData(JSONData: context.state.data)

            // DynamicIsland
            return DynamicIsland {
                // Expanded UI goes here.  Compose the expanded UI through
                // various regions, like leading/trailing/center/bottom
                DynamicIslandExpandedRegion(.leading) {
                    Text("Leading")
                }
                DynamicIslandExpandedRegion(.trailing) {
                    Text("Trailing")
                }
                DynamicIslandExpandedRegion(.bottom) {
                    // Show data from flutter
                    Text(data?.text ?? "")
                }
            } compactLeading: {
                Text("L")
            } compactTrailing: {
                Text("T")
            } minimal: {
                Text("Min")
            }
            .keylineTint(Color.red)
        }
    }
}
   ```

4. APIs

   ```
   import 'package:flutter_live_activities/flutter_live_activities.dart';
   ...
   final FlutterLiveActivities _liveActivities = FlutterLiveActivities();
   String? _activityId;
```


