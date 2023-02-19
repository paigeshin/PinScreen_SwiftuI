# PinScreen_SwiftuI

```swift
//
//  ContentView.swift
//  LockScreen
//
//  Created by paige shin on 2023/02/20.
//

import SwiftUI

struct ContentView: View {
    var body: some View {
        NavigationView {
            Home()
        } //: NAVIGATION VIEW
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

struct Home: View {
    
    @State var unlocked = false
    
    var body: some View {
        ZStack {
            
            if self.unlocked {
                Text("App Unlocked")
                    .font(.title)
                    .fontWeight(.heavy)
            } else {
                // Lockscreen....
                LockScreen(unlocked: self.$unlocked)
            }
            
        } //: ZSTACK
        .preferredColorScheme(.dark)
    }
    
}

struct LockScreen: View {
    
    @State var password = ""
    
    // You can change it when user clicks reset password...
    // Appstorage => UserDefaults....
    @AppStorage("lock_password") var key = "5454"
    
    @Binding var unlocked: Bool
    @State var wrongPassword = false

    
    var body: some View {
        VStack {
            
            HStack {
                
                Spacer(minLength: 0)
                
                Menu(content: {
                    
                    Label(title: { Text("Help") },
                          icon: { Image(systemName: "info.circle.fill") })
                    .onTapGesture {
                        
                    }
                    
                    Label(title: { Text("Reset Password") },
                          icon: { Image(systemName: "key.fill") })
                    .onTapGesture {
                        
                    }
                    
                }, label: {
                    Image(systemName: "line.3.horizontal")
                        .renderingMode(.template)
                        .resizable()
                        .frame(width: 19, height: 19)
                        .foregroundColor(.white)
                        .padding()
                }) //: MENU
                
            } //: HSTACK
            .padding(.leading)
            
            Image(systemName: "heart.fill")
                .resizable()
                .frame(width: 95, height: 95)
                .padding(.top, 20)
            
            Text("Enter Pin to unlock")
                .font(.title2)
                .fontWeight(.heavy)
                .padding(.top, 20)
            
            HStack(spacing: 22) {
                
                // Password Circle View
                ForEach(0..<4, id: \.self) { index in
                    PasswordView(
                        index: index,
                        password: self.$password,
                        key: self.$key,
                        unlocked: self.$unlocked,
                        wrongPassword: self.$wrongPassword
                    )
                } //: FOREACH
                
            } //: HSTACK
            .padding(.top, 30)
            
            // KeyPad...
            Spacer(minLength: 0)
            
            Text(self.wrongPassword ? "Incorrect Pim" : "")
                .foregroundColor(.red)
                .fontWeight(.heavy)
            
            Spacer(minLength: 0)
            
            LazyVGrid(columns: Array(repeating: GridItem(.flexible()), count: 3), spacing: 15) {
                
                // Password Button ....
                ForEach(1...9, id: \.self) { value in
                    PasswordButton(
                        value: "\(value)",
                        password: self.$password,
                        key: self.$key,
                        unlocked: self.$unlocked,
                        wrongPassword: self.$wrongPassword
                    )
                }
                
                PasswordButton(
                    value: "delete.fill",
                    password: self.$password,
                    key: self.$key,
                    unlocked: self.$unlocked,
                    wrongPassword: self.$wrongPassword
                )
                
                PasswordButton(
                    value: "0",
                    password: self.$password,
                    key: self.$key,
                    unlocked: self.$unlocked,
                    wrongPassword: self.$wrongPassword
                )
                
            } //: LAZYVGRID
            .padding(.bottom)
            
            Spacer(minLength: 0)
            
        } //: VSTACK
        .navigationTitle("")
        .navigationBarHidden(true)
    }
    
}

struct PasswordView: View {
    
    var index: Int
    
    @Binding var password: String
    @Binding var key: String
    @Binding var unlocked: Bool
    @Binding var wrongPassword: Bool
    
    var body: some View {
        ZStack {
            Circle()
                .stroke(Color.white, lineWidth: 2)
                .frame(width: 30, height: 30)
            
            // checking wheter it is typed...
            
            if self.password.count > self.index {
                Circle()
                    .fill(Color.white)
                    .frame(width: 30, height: 30)
            }
            
        } //: ZSTACK
    }
    
}

struct PasswordButton: View {
    
    var value: String
    @Binding var password: String
    @Binding var key: String
    @Binding var unlocked: Bool
    @Binding var wrongPassword: Bool
    
    var body: some View {
        
        Button {
            self.setPassword()
        } label: {
            
            VStack {
                
                if self.value.count > 1 {
                    // Image ...
                    Image(systemName: "delete.left")
                        .font(.system(size: 24))
                        .foregroundColor(.white)
                } else {
                    Text(self.value)
                        .font(.title)
                        .foregroundColor(.white)
                }
            } //: VSTGACK
            .padding()
            
        }

        
    }
    
    func setPassword() {
        
        withAnimation {
            
            // checking if backspace pressed...
            if self.value.count > 1 {
                if self.password.count != 0 {
                    self.password.removeLast()
                }
            } else {
                if self.password.count != 4 {
                    self.password.append(self.value)
                    
                    // Delay Animation...
                    
                    DispatchQueue.main.asyncAfter(deadline: .now() + 0.3) {
                        withAnimation {
                            if self.password.count == 4 {
                                if self.password == self.key {
                                    self.unlocked = true
                                } else {
                                    self.wrongPassword = true
                                    self.password.removeAll()
                                }
                            }
                        }
                    }
                    
                    
                }
            }
            
        }
        
    }
    
    
}

```
